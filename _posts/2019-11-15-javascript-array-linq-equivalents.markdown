---
layout: post
title:  "JavaScript Array LINQ Equivalents"
categories: "array linq"
---

Following are some of JavaScript Array equivalent methods of LINQ.

<table border="1" cellspacing="5" cellpadding="5" style="margin-bottom: 10px;">
	<tr bgcolor="AFEEEE">
		<th width="300">LINQ</th>
		<th width="300">JavaScript Array</th>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Filter</td>
	</tr>
	<tr>
		<td>Where</td>
		<td>Array.prototype.filter</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Order</td>
	</tr>
	<tr>
		<td>OrderBy</td>
		<td>Array.prototype.sort</td>
	</tr>
	<tr>
		<td>OrderByDescending</td>
		<td>Array.prototype.sort</td>
	</tr>
	<tr>
		<td>Reverse</td>
		<td>Array.prototype.reverse</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Project</td>
	</tr>
	<tr>
		<td>Select</td>
		<td>Array.prototype.map</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Aggregate</td>
	</tr>
	<tr>
		<td>Count</td>
		<td>Array.length</td>
	</tr>
	<tr>
		<td>Aggregate</td>
		<td>Array.prototype.reduce</td>
	</tr>
	<tr>
		<td colspan="2" bgcolor="FFEBCD">Find</td>
	</tr>
	<tr>
		<td>Contains</td>
		<td>Array.prototype.some</td>
	</tr>
	<tr>
		<td>Any</td>
		<td>Array.prototype.some</td>
	</tr>
	<tr>
		<td>All</td>
		<td>Array.prototype.every</td>
	</tr>
</table>

However, LINQ query is actually ***Deferred Execution***, which is the exact idea of **JavaScript ES6 Iterator and Generator**. Here is how I implement **Where** and **Select** with JavaScript **yield**.

```javascript
class LinqArray {
	constructor(iterator) {
		this.iterator = iterator;
	}

	static from(array) {
		return new LinqArray(function () {
			return array;
		});
	}

	where(predicate) {
		const { iterator } = this;
		return new LinqArray(function* () {
			for (const item of iterator()) {
				if (predicate(item)) {
					yield item;
				}
			}
		});
	};

	select(project) {
		const { iterator } = this;
		return new LinqArray(function* () {
			for (const item of iterator()) {
				yield project(item);
			}
		});
	};
}
```

The hardest part of the above codes is how to make the iterators **chainable**. Now, you can use it as below:

```javascript
const iter = LinqArray
	.from([3,4,6,7])
	.where(n => n % 2 === 0)
	.select(n => n * 2)
	.iterator();

for (const n of iter) {
	console.log(n);
}
```