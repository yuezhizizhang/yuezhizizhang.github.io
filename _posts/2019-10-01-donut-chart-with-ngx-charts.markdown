---
layout: post
title:  "Donut Chart with ngx-charts"
categories: "swimlane ngx-charts"
---

[ngx-charts](https://github.com/swimlane/ngx-charts) is an awesome charting framework for Angular. It supports a vast variety of chart types. [Here](https://swimlane.github.io/ngx-charts/) is the examples gallery of various beautiful charts.

Even though ngx-charts is an amazing library, the document of the library is sort of poorly organized.

Taking my experience for instance, I wanted to draw a donut chart with ngx-charts. By browsing through the [charts gallery](https://swimlane.github.io/ngx-charts/), my first impression was [AdvancedPieChartComponent](https://swimlane.github.io/ngx-charts/#/ngx-charts/advanced-pie-chart) is the right donut chart component. Then I dive into the documents and source codes directly. I spent a lot time investigating how to display the labels around the donut chart arc. However, until I read the source codes of [PieChartComponent](https://github.com/swimlane/ngx-charts/blob/master/src/pie-chart/pie-chart.component.ts), I realized that pie chart component has a property called **doughnut**, which could easily turn a pie chart into a donut chart.

> [PieChartComponent](https://github.com/swimlane/ngx-charts/blob/master/src/pie-chart/pie-chart.component.ts)
```typescript
@Input() doughnut = false;
```

I revisit the charts gallery and the ngx-charts documents afterwards. I find out I have missed two things.

**1. Play with [Charts Gallery](https://swimlane.github.io/ngx-charts/)**

[charts gallery](https://swimlane.github.io/ngx-charts/) is a live demo. For each chart type example, there is a **DATA** section and a **OPTIONS** section in the left menu. Do expand these sections. You could toggle the options or change the data to see how the chart is affected. It's much more efficient than reading the documents or source codes directly. Play with the examples before you proceed to the documents or source codes.

**2. [docs-test](https://swimlane.gitbook.io/ngx-charts/v/docs-test/) version is more valuable**

I found out there are 2 versions of docs, one is **[Primary version](https://swimlane.gitbook.io/ngx-charts/)**, the other is **[docs-test](https://swimlane.gitbook.io/ngx-charts/v/docs-test/)**. Even though the gallery links to the Primary version, however, it is **docs-test** version which contains a Plunker live example for each chart type.

It's very helpful to play with the Pluker live demo. Take [PieChartComponent](https://swimlane.gitbook.io/ngx-charts/v/docs-test/examples/pie-charts/pie-chart) for example, in the live example, you are able to find a lot of useful properties:

```typescript
// [Width, height]
view = [700, 400];

// Display legend or not
legend = true;

// Colors of each arc
scheme = {
  domain: ['#5AA454', '#A10A28', '#C7B42C', '#AAAAAA']
};

// Display labels around the pie circle or not
labels = true;

// Pie chart or Donut chart
doughnut = false;
```

The other useful properties I found are:

```typescript
// Legend position: 'right', 'bottom'
legendPosition = "'bottom'";

// The title above the legend box
legendTitle = "''";

// top, left, bottom, right margins
margins = [10, 20, 10, 20];

// Disable tootip or not
tooltipDisabled = true;
```

My only complaint about the library is it's not flexible enough to customize the legend, the labels and the tooltips of the charts. All of the them rely on the **name** field in the data. Hence, it would be very hard if you want to display stuff other than name in the chart. For instance, my data has a label field, which I hope to display around the pie chart as labels, the only way to do it is:

```typescript
<ngx-charts-pie-chart
  [results]="series"
  [labelFormatting]="pieChartLabel.bind(this, series)"
</ngx-charts-pie-chart>


series = [{
    name: 'Retired',
    value: 20,
    label: '20%'
}, {
    name: 'Employed',
    value: 80,
    label: '80%'
}];

pieChartLabel(series: any[], name: string): string {
    const item = series.filter(data => data.name === name);
    if (item.length > 0) {
        return item[0].label;
    }
    return name;
}
```

For labels, at least there is a **labelFormatting** method for you to customize it. However, for the legend and tooltips, the customize method is not exposed in the PieChartComponent at all.

<iframe src="https://stackblitz.com/edit/angular-qt6kfw?embed=1&file=src/app/app.component.ts" style="width: 100%; height: 400px;"></iframe>