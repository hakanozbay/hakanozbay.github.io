---
layout: post
title:  "Java & Spring: Handling database errors"
date:   2017-03-25 18:11:00
---

During database I/O operations, there are common errors occurring frequently that are not properly handled, which jeopardizes the continuing operation of your software, causing manual intervention to resolve issues. Defensive programming techniques should be implemented for self recovery from these errors, making your software behave in a predicted manner. This allows for greater control of your software, a more intelligent workflow, reduction in errors and manual intervention, saving time for your business and reducing support efforts.

The most frequent and unhandled errors that I have come across are deadlocks; duplicates exisitng in the table the software is writing to; data integrity violation (such as null data in non nullable columns); Badly formatted SQL queries.

Identifying these errors is not difficult with the help of the Spring JDBC library, using their [SQLErrorCodes][] and [SQLErrorCodesFactory][] classes and loading the relevant database error codes from their [sql-error-codes.xml][] resource file. The sql-error-codes.xml file has error codes for all popular database technologies.

Assuming we create a wrapper class called `DatabaseExceptionUtilities` we can use load in the relevant database error codes with this snippet of code:

{% highlight java %}
@Component
public class DatabaseExceptionUtilities 
{
	@Autowired
	DataSource dataSource;
	
	private SQLErrorCodes sqlErrorCodes;

	private SQLErrorCodes getSqlErrorCodes()
	{
		if (sqlErrorCodes == null)
			sqlErrorCodes = SQLErrorCodesFactory.getInstance().getErrorCodes(dataSource);
		
		return sqlErrorCodes;
	}
...
...
...
}
{% endhighlight %}

The `dataSource` variable is a dependency injection from an exisitng dataSource bean that would be created in a Spring configuration file. Based on this the relevant database error codes are loaded and provided. 

The remainder of the class defines wrapper methods that are error specific which can be called directly by utilizing classes:

{% highlight java %}
public boolean isExceptionADeadlock(SQLException exception)
{
	String errorCode = getErrorCode(exception);
	String sqlState = getSqlState(exception);
	return Arrays.stream(getSqlErrorCodes().getDeadlockLoserCodes()).anyMatch(x -> x.equalsIgnoreCase(errorCode) || x.equalsIgnoreCase(sqlState));
}
	
public boolean isExceptionADuplicate(SQLException exception)
{
	String errorCode = getErrorCode(exception);
	String sqlState = getSqlState(exception);
	return Arrays.stream(getSqlErrorCodes().getDuplicateKeyCodes()).anyMatch(x -> x.equalsIgnoreCase(errorCode) || x.equalsIgnoreCase(sqlState));
}
	
public boolean isExceptionADataIntegrityViolation(SQLException exception)
{
	String errorCode = getErrorCode(exception);
	String sqlState = getSqlState(exception);
	return Arrays.stream(getSqlErrorCodes().getDataIntegrityViolationCodes()).anyMatch(x -> x.equalsIgnoreCase(errorCode) || x.equalsIgnoreCase(sqlState));
}
	
public boolean isExceptionBadGrammerSQL(SQLException exception)
{
	String errorCode = getErrorCode(exception);
	String sqlState = getSqlState(exception);
	return Arrays.stream(getSqlErrorCodes().getBadSqlGrammarCodes()).anyMatch(x -> x.equalsIgnoreCase(errorCode) || x.equalsIgnoreCase(sqlState));
}
	
private String getErrorCode(SQLException exception)
{
	return Integer.toString(exception.getErrorCode());
}
	
private String getSqlState(SQLException exception) 
{
	return exception.getSQLState();
}
{% endhighlight %}

The `DatabaseExceptionUtilities` class can now be used in any class that performs database I/O opetations, where the relevant exception type can be caught, examined and identified:


{% highlight java %}
@Autowired
DatabaseExceptionUtilities databaseExceptionUtilities;
	
public void doSomethingThatInvolvesADatabase()
{
	try
	{
		...
		...
		...
	}
	catch(SQLException e)
	{
		handleDatabaseException(e);
	}
}

protected void handleDatabaseException(SQLException exception) 
{
	if (databaseExceptionUtilities.isExceptionBadGrammerSQL(exception))
		handleBadGrammarException();
		
	else if (databaseExceptionUtilities.isExceptionADuplicate(exception))
		handleDuplicateException();
		
	else if (databaseExceptionUtilities.isExceptionADeadlock(exception))
		handleDeadlockException();
		
	else if (databaseExceptionUtilities.isExceptionADataIntegrityViolation(exception))
		handleDataIntegrityViolationException();
			
}
{% endhighlight %}

The `handleBadGrammarException()` , `handleDuplicateException()` , `handleDeadlockException()` and `handleDataIntegrityViolationException()` are the handler methods that you define to determine what course of action to take.

Writing error handling code for database interaction errors can be straight forward and written cleanly when leveraging existing frameworks effectively.

_I have written an example demonstration of this implementation in my [Database error handling][]{:target="_blank"} code repository._


[SQLErrorCodes]: http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/support/SQLErrorCodes.html
[SQLErrorCodesFactory]:http://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/jdbc/support/SQLErrorCodesFactory.html
[sql-error-codes.xml]: https://github.com/spring-projects/spring-framework/blob/master/spring-jdbc/src/main/resources/org/springframework/jdbc/support/sql-error-codes.xml
[Database error handling]: https://github.com/hakanozbay/database-error-handling