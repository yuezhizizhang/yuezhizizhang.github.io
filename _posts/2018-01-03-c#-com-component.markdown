---
layout: post
title:  "C# COM Component"
categories: "C# COM Interop .NET"
---

The document below tells you every detail about how to make and register a C# .NET Managed Assembly for COM Interop:

**[How to call a managed DLL from native Visual C++ code in Visual Studio.NET or in Visual Studio 2005](https://support.microsoft.com/en-us/help/828736/how-to-call-a-managed-dll-from-native-visual-c-code-in-visual-studio-n)**

The only defect is that it is targeted at Visual Studio 2005. Hence, in Visual Studio 2013, there are some minor differences.

* **How to open Visual Studio 2013 Command Prompt**

Select **Tools** -> **External Tools**, Click **Add**. Fill in the fields as below:

<table style="margin-bottom: 10px;">
	<tr>
		<th style="text-align: left;">Title</th>
		<td>VS2013 Native Tools-Command Prompt</td>
	</tr>
	<tr>
		<th style="text-align: left;">Commands</th>
		<td>C:\Windows\System32\cmd.exe</td>
	</tr>
	<tr>
		<th style="text-align: left;">Arguments</th>
		<td>/k "C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\Tools\VsDevCmd.bat"</td>
	</tr>
	<tr>
		<th style="text-align: left;">Initial directory</th>
		<td>Select what you prefer</td>
	</tr>
</table>

Thus, you can access command prompt under the **Tools** Menu. The other way to do it is simply opening a Command Console. Then run the batch file "VsDevCmd.bat" in the console. It works the same way.

* **Assembly Info and Signing**

In the AssemblyInfo.cs file, replace the following lines:

```c#
[assembly: ComVisible(true)]
[assembly: AssemblyDelaySign(false)]
```

Then, select the project, open **Properties**. In the **Signing** tab, choose the SNK file.

* **Register DLL for COM Interop**

 Open a Command Console as Administrator, run the command prompt:

```cmd
 C:\Windows\Microsoft.NET\Framework\v4.0.30319\RegAsm.exe XXX.dll /tlb:XXX.tlb /codebase
 ```

 My .NET framework version is v4.0.30319.

 * **Marshaling**

 This MSDN doc __[Interop Marshaling](https://docs.microsoft.com/en-us/dotnet/framework/interop/interop-marshaling)__ talks about Marshaling. In my own usecase, I have met the conversion table below:

 <table>
 	<tr>
 		<th width="100" style="text-align: left;">.NET</th>
 		<th style="text-align: left;">COM client</th>
 	</tr>
 	<tr>
 		<td>bool</td>
 		<td>VARIANT_BOOL</td>
 	</tr>
 	<tr>
 		<td>string</td>
 		<td>BSTR</td>
 	</tr>
 </table>

 In C++ native codes, to define a BSTR, the code snippet is:

 ```c++
 BSTR name = ::SysAllocString(L"The string");
 ```