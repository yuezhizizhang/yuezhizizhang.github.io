---
layout: post
title:  "Minify JavaScript with UglifyJS"
categories: "javascript"
---

UglifyJS is a JavaScript minifying tool, which runs on NodeJS. Compared with other popular JavaScript minifiers, such as YUI Compressor and Google Closure, a remarkable advantage of using UglifyJS is that it's available as a native command. To utilize UglifyJS, we need to install NodeJS first and then the UglifyJS module.

Here is the guideline on installing NodeJS [https://github.com/joyent/node/wiki/Installation](https://github.com/joyent/node/wiki/Installation). After successful installation, node server starts running.

Then, install UglifyJS module [https://github.com/mishoo/UglifyJS#Install](https://github.com/mishoo/UglifyJS#Install) on the node server. After adding the UglifyJS path into PATH, we are able to use it via command prompt:

$ uglifyjs -nc -o [output js] [input js]

Extremely easy, isn't it? Following is a small test comparing the compressing efficiency of different minifiers:

<table border="1" cellspacing="0" cellpadding="5">
<tr>
	<th></th>
	<th bgcolor="9ACD32">Original</th>
	<th bgcolor="9ACD32">Uglify</th>
	<th bgcolor="9ACD32">Closure</th>
	<th bgcolor="9ACD32">YUI Compressor</th>
</tr>
<tr>
	<td bgcolor="FFD700">size</td>
	<td>193.5 KB</td>
	<td>62.3 KB</td>
	<td>61.3 KB</td>
	<td>64.4 KB</td>
</tr>
</table>