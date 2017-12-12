---
layout: post
title:  "Performance Counters"
categories: "c# windows"
---

**1. What are Performance Counters**

I strongly recommend you read two in-depth posts about Peformance Counter:

* [Windows Performance Counters Explained](http://www.appadmintools.com/documents/windows-performance-counters-explained/)
* [Windows Performance Monitor Disk Counters Explained](https://blogs.technet.microsoft.com/askcore/2012/03/16/windows-performance-monitor-disk-counters-explained/)

**2. List Performance Counters**

How to list all of the performance counters available on your computer? Run the following command in the command console:

```cmd
> TypePerf.exe –q
```

**3. Restore Performance Counters**

If some counters are missing from your computer, how to rebuild them? Run the command console as Administrator:

```cmd
> cd C:\WINDOWS\system32
> lodctr.exe /R
> lodctr.exe /R
> cd C:\Windows\SysWOW64
> lodctr.exe /R
```

If you meet errors while running "lodcrt.exe", the best try is to run it once again.

**4. Common Performance Counters**

In the table below, I've listed some of the usally used counters and the properties to create them in c#:

<table class="table">
	<thead>
		<tr>
			<th>Benchmark</th>
			<th>categoryName</th>
			<th>counterName</th>
			<th>instanceName</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>Processor % Usage</td>
			<td>"Processor"</td>
			<td>"% Processor Time"</td>
			<td>"_Total"</td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Memory Available Bytes</td>
			<td>"Memory"</td>
			<td>"Available Bytes"</td>
			<td>""</td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Disk % Idle Time</td>
			<td>"PhysicalDisk"</td>
			<td>"% Idle Time"</td>
			<td>"_Total"</td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Disk Read Bytes/sec</td>
			<td>"PhysicalDisk"</td>
			<td>"Disk Read Bytes/sec"</td>
			<td>"_Total"</td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Disk Write Bytes/sec</td>
			<td>"PhysicalDisk"</td>
			<td>"Disk Write Bytes/sec"</td>
			<td>"_Total"</td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Network Bytes Received/sec</td>
			<td>"Network Interface"</td>
			<td>"Bytes Received/sec"</td>
			<td><i>NID</i></td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Network Bytes Sent/sec</td>
			<td>"Network Interface"</td>
			<td>"Bytes Sent/sec"</td>
			<td><i>NID</i></td>
		</tr>
	</tbody>
	<tbody>
		<tr>
			<td>Network Throughput</td>
			<td>"Network Interface"</td>
			<td>"Bytes Total/sec"</td>
			<td><i>NID</i></td>
		</tr>
	</tbody>
</table>

**NID** refers to the name of the network card. To print out the network cards on your computer:

```c#
public void printNetworkCards()
{
    PerformanceCounterCategory category = new PerformanceCounterCategory("Network Interface");
    string[] names = category.GetInstanceNames();

    foreach (string name in names)
    {
        Console.WriteLine(name);
    }
}
```