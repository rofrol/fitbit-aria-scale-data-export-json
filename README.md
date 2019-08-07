# How to download all weight measurements from fitbit.com for Aria Scale in JSON format

## Problem

When you go to https://www.fitbit.com/ and click gear icon on the right, you can select `Aria Scale` if you connected one.

Then on the left you can click `Data Export`, Select `Custom` but `Start date` cannot be less then 31 days.

But you can get all your measurements in JSON format other way

##  Solution

To get measurements go to https://www.fitbit.com/weight or by `Log > Weight`.

To get all records, click subsequently `Load More`.

To extract save all records as json open dev console by pressing F12, go to console tab or press Escape, and type this:

``` javascript
function download(data, filename, type) {
    var file = new Blob([data], {type: type});
    if (window.navigator.msSaveOrOpenBlob) // IE10+
        window.navigator.msSaveOrOpenBlob(file, filename);
    else { // Others
        var a = document.createElement("a"),
                url = URL.createObjectURL(file);
        a.href = url;
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        setTimeout(function() {
            document.body.removeChild(a);
            window.URL.revokeObjectURL(url);
        }, 0);
    }
}

download(JSON.stringify(Array.from(document.querySelectorAll('[data-test-qa=weight-list-item]'), item =>
	({
	    date: item.querySelector('[data-test-qa="item-date-time"]').innerText,
	    fat: item.querySelector('.body-fat-text').innerText,
		weight: item.querySelector('[data-test-qa=body-weight-list]').innerText
	}))), 'weight.json', 'application/json');
```

and press `Enter`.

- download function from https://stackoverflow.com/questions/13405129/javascript-create-and-save-file/30832210#30832210
- for stringify https://stackoverflow.com/questions/2295496/convert-array-to-json/2295515#2295515
- to process first three dates which are in format Today, Mon, Tue etc. Not done yet https://stackoverflow.com/questions/13274166/how-do-i-pop-off-the-first-array-element-with-jquery/13274196#13274196
- to convert array iterator to array https://stackoverflow.com/questions/43885365/using-map-on-an-iterator/43885505#43885505
- to get array iterator on values use `.values()` https://developer.mozilla.org/en-US/docs/Web/API/NodeList#Example
- no need to use `.values()` because `NodeList` is iterable `x = document.querySelectorAll('body'); x[Symbol.iterator] === x.values` - thanks to https://github.com/P0lip
- example that you can run `querySelector` on nodeList items https://stackoverflow.com/questions/6791112/how-to-filter-elements-returned-by-queryselectorall/6791385#6791385
