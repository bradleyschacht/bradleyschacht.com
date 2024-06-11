---
title: "Batch Files, Command Lines"
date: "2011-02-24"
categories: 
  - "windows"
---

The problem once you get over the learning curve of SSIS is not the tool; it’s all the things that work with SSIS and that can be used by/with SSIS.  For instance, there are a ton of things that you can do with an Execute Process task.  The other thing is trying to make your packages dynamic. Combining those two hurdles can sometimes be a lot to overcome. I ran into one such instance recently.  There was a set of packages that called batch files in order to do some downloading from an FTP.  The issues here were two-fold:  I had to allow it to download and access files on a network share then it also had to allow that location to be changed from outside the package so the batch files didn’t have to be opened in every environment.  Let’s address each of these separately. Issue Number 1: Store, access and use batch files with a network share If you try to do anything using the command prompt that deals with UNC paths you run into a small problem.  You can’t do it.  This becomes a big problem when everything being done is dealing with file shares.  The error will be something like that in the screen shot here:  CMD does not support UNC paths as current directories. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/batchfiles1.png) After some diligent use of a wonderful free tool, www.google.com, I came across something that was easily the niftiest thing I have discovered in quite a while.  There is a command that you can use to map any network location to a drive letter: pushd {NetwrokLocation}.  The cool part is that you don’t even have to know what drive letters are not taken up.  It will start with Z and work its way backward through the alphabet to find an available drive letter.  The other side of that is popd {MappedDriveLetter} to release that mapping.  But that isn’t even required because as soon as the session is finished the mapping is released anyway.  So now I can map PWBradSSIS to a drive letter and use it in a batch file!  The advantage of this is the following piece of code does not work: CD PWBradSSIS FTP -i -s:FTPDownload.script But this will work: pushd PWBradSSIS FTP -i -s:FTPDownload.script This is a great way to be able to use network locations in your batch files and in execute process tasks.  When you do this can use all the regular commands just like any other location.  Give it a try.  Map a location with pushd NetworkLocationHere and then navigate around using the cd command or get the directory contents with a simple little dir.  The next screen shot will give you a short visual of how to use the commands. ![](https://images.bradleyschacht.com/wp-content/uploads/2011/12/batchfiles2.png) Issue Number 2: Making your batch files configurable We will cover this in another blog posting for tomorrow.  Let’s not get too carried away.  I’m sure you want to go play with pushd and popd now anyway.  Have fun and please let me know if this is helpful to you or useful to you in any way, shape or form.  Check back tomorrow for part two: Configurability.