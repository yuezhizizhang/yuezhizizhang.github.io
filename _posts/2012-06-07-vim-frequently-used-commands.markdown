---
layout: post
title:  "vim Frequently Used Commands"
categories: "linux"
---

<table border="1" cellspacing="0" cellpadding="5">
	<tr bgcolor="AFEEEE">
		<th width="200">Command</th>
		<th>Description</th>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Save and Quit</td>
	</tr>
	<tr>
		<td>:w</td>
		<td>Save changes</td>
	</tr>
	<tr>
		<td>:wq   or   :x</td>
		<td>Save changes and quit</td>
	</tr>
	<tr>
		<td>:q!</td>
		<td>Quit without saving</td>
	</tr>
	<tr>
		<td>u</td>
		<td>Undo last change</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Move Cursor</td>
	</tr>
	<tr>
		<td>^</td>
		<td>Move cursor to the beginning of current line</td>
	</tr>
	<tr>
		<td>$</td>
		<td>Move cursor to the end of current line</td>
	</tr>
	<tr>
		<td>w</td>
		<td>Move cursor to the beginning of next word</td>
	</tr>
	<tr>
		<td>b</td>
		<td>Move cursor to the beginning of preceding word</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Delete</td>
	</tr>
	<tr>
		<td>dw</td>
		<td>Delete single word beginning with the character under cursor</td>
	</tr>
	<tr>
		<td>dd</td>
		<td>Delete current line</td>
	</tr>
	<tr>
		<td>d$</td>
		<td>Delete the remaining of line beginning with the character under cursor</td>
	</tr>
	<tr>
		<td>:1,$d$</td>
		<td>Delete all the lines</td>
	</tr>
	<tr>
		<td>:1,Nd</td>
		<td>Delete 1 to N lines</td>
	</tr>
	<tr>
		<td>:Ndd</td>
		<td>Delete the next N lines, including the current line</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Copy and Paste</td>
	</tr>
	<tr>
		<td>yw</td>
		<td>Copy a word</td>
	</tr>
	<tr>
		<td>yy</td>
		<td>Copy current line</td>
	</tr>
	<tr>
		<td>Nyy</td>
		<td>Copy the next N lines, including the current line</td>
	</tr>
	<tr>
		<td>:1,Ny</td>
		<td>Copy 1 to N lines</td>
	</tr>
	<tr>
		<td>p</td>
		<td>Paste buffer after the current line</td>
	</tr>
	<tr>
		<td>:r filename</td>
		<td>Read file named filename and insert after current line</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Search and Replace</td>
	</tr>
	<tr>
		<td>/string</td>
		<td>Search for a string. Press n, cursor moves to the next occurrence; Press N, cursor moves to the preceding occurrence.</td>
	</tr>
	<tr>
		<td>:s/old/new/g</td>
		<td>Find each occurrence of ‘old’ on the current line and replace it with ‘new’</td>
	</tr>
	<tr>
		<td>:%s/old/new/g</td>
		<td>Find each occurrence of ‘old’ throughout the file and replace it with ‘new’</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Display</td>
	</tr>
	<tr>
		<td>:set number</td>
		<td>Display line numbers</td>
	</tr>
	<tr>
		<td>:vsplit filename</td>
		<td>Split vi vertically and display file named filename on the left half</td>
	</tr>
	<tr>
		<td>ctrl-w ctrl-w</td>
		<td>Move cursor between split screens</td>
	</tr>
</table>