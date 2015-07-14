---
layout: post
title:  "Removing DOS line ending markers on files to be used on a UNIX environment"
date:   2013-06-27 13:47:58
categories: jekyll update
---

If, like me, you're a developer coding on Windows but run your application on a UNIX environment, you will face the problem of continually having the DOS marker "^M" appearing in most of your files. These will be noticed when you view files on UNIX once deployed, which can be both a nuisance and create problems in correct application execution.
 
A common solution for this has been to run the dos2unix command manually for each file like so:
> dos2unix filename filename

Albeit this works, it is quite a repetitive, manual and potentially erroneous task. Also, you will need to do this every time when you deploy to the server.
 
I have devised a solution that will eradicate these markers automatically upon committing files to a **Subversion** version control environment (svn). The key is to utilize the svn property: `svn:eol-style`.  This property will automatically apply end of line formatting of your choice to any file type that you commit. This property must be set in your local subversion configuration file to reflect UNIX line ending standards in this case. For reference, the UNIX end of line style is 'LF' (for Windows it is 'CRLF').
 
To enable this feature you must do the following on your Windows development computer:
 
1. Open the subversion config file: `C:\Users\<USER_ID>\AppData\Roaming\Subversion\config`
2. Uncomment the `enable-auto-props = yes` property (by removing the '#' symbol next to it)
3. In the `[auto-props]` section (at the bottom), for example you can add:

{% highlight xml %}
*.csh = svn:eol-style=LF
*.java = svn:eol-style=LF
*.properties = svn:eol-style=LF
*.sh = svn:eol-style=LF
*.xml = svn:eol-style=LF
*.xsd = svn:eol-style=LF
{% endhighlight %}

Once you save the file, the declared file extensions will have the 'LF' line ending format automatically applied when you commit them. You may also use the wildcard `*.* = svn:eol-style=LF` if you want to apply for all files, regardless of extension.
 
One caveat of this solution however, is that this property is applied for new files that will be committed for the first time. For existing files already checked in, you must execute this property on the files locally and then commit them. You can do this in 2 different ways:
 
###On the command line:
 
Navigate to your project directory and at the top level, execute the following svn command:

`
svn propset -R svn:eol-style LF *
`
 
This setting will be applied to all files under the project. If you want to do it for specific file types only, you have to navigate to folders containing those file types and execute the same command with a modified parameter, for example:

`
svn propset -R svn:eol-style LF *.java
`
 
###In the IDE (Eclipse + Subclipse):
 
Right click on your project folder/a subfolder/a file -> Team -> Set Property...

set property menu option.png
 
Then fill in the following screen like so:
 
set property.png
 
Once this action is performed, proceed to commit the files.
 
There's also an extra setting you can enable in Eclipse: Specify which operating system line ending format new files that you create in the IDE should adopt. You can do this by selecting the Window option menu -> Preferences -> General -> Workspace and setting the "New text file line delimiter" as UNIX:
 
unix delimiter.png
 
 
With this in place now, you will no longer need to cater for the continual overhead of manually converting DOS line endings to the UNIX line ending format for your project files. The UNIX line ending format is compatible to be used in Windows without any issues. This definitely has reduced some headaches for me!