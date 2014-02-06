#!/usr/bin/env phantomjs

var page = require('webpage').create(),
    system = require('system'),
    fs = require('fs'),
    address, output, size, f, content;


if (system.args.length < 4 || system.args.length > 6) {
    console.log('Usage: rasterize.js url geojson output [paperwidth*paperheight|paperformat] [zoom]');
    console.log('  paper (pdf output) examples: "5in*7.5in", "10cm*20cm", "A4", "Letter"');
    console.log('  image (png/jpg output) examples: "1920px" entire page, window width 1920px');
    console.log('                                   "800px*600px" window, clipped to 800x600');
    phantom.exit(1);
} else {
    address = system.args[1];
    geojsonfname = system.args[2]
    output = system.args[3];
    page.viewportSize = { width: 600, height: 600 };
    if (system.args.length > 4 && system.args[3].substr(-4) === ".pdf") {
        size = system.args[3].split('*');
        page.paperSize = size.length === 2 ? { width: size[0], height: size[1], margin: '0px' }
                                           : { format: system.args[4], orientation: 'portrait', margin: '1cm' };
   } else if (system.args.length > 4 && system.args[4].substr(-2) === "px") {
        size = system.args[4].split('*');
        if (size.length === 2) {
            pageWidth = parseInt(size[0], 10);
            pageHeight = parseInt(size[1], 10);
            page.viewportSize = { width: pageWidth, height: pageHeight };
            page.clipRect = { top: 0, left: 0, width: pageWidth, height: pageHeight };
        } else {
            console.log("size:", system.args[4]);
            pageWidth = parseInt(system.args[4], 10);
            pageHeight = parseInt(pageWidth * 3/4, 10); // it's as good an assumption as any
            console.log ("pageHeight:",pageHeight);
            page.viewportSize = { width: pageWidth, height: pageHeight };
        }
    }
    try {
        f = fs.open(geojsonfname, "r");
        content = f.read();
    } catch (e) {
        console.log(e);
    }

    if (f) {
        f.close();
    }

    if (content) {
        //var resultObject = JSON.parse(content);
        var resultObject = content;
    }

    page.onLoadFinished = function(status) {
        if (status !== 'success') {
            console.log('Unable to load the address!');
            phantom.exit();
        } else {
            var stringa = "function () { markerLayer.setGeoJSON("+resultObject+"); map.fitBounds(markerLayer.getBounds()); }";
            //console.log(stringa);
            page.evaluateJavaScript(stringa);
            window.setTimeout(function () {
                page.render(output);
                phantom.exit();
            }, 1000);
        }
    };

    page.open(address);
}
