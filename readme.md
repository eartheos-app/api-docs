![alt tag](./assets/eartheos-dev_docs_github_banner.png)
# Eartheos for Developers

## Overview

Our mission is to give users perspective on global issues by transforming the Earth into a storytelling and educational platform. Developers, institutions, and students alike use the Eartheos app to visualize a topic as it relates to different locations around the world. With the globe at the center of our experience, we seek to transport users to the international space station where they can explore our planet through layers of information.

## Testing Data

To test the way your data renders in the Eartheos app, we recommend setting up a web server on your local machine to serve the necessary JSON files.  If you don't have one handy, we recommend the [http-server](https://github.com/indexzero/http-server) package.

Once you have your server running, open Eartheos on your iOS device and scroll to the "Add Your Data" card.  Tap "Learn More" and follow the on-screen instructions.

![alt tag](./assets/18-Card-Dev.jpg) ![alt tag](./assets/19-Card-Full_Screen_Scroll.jpg)

## Examples

We **strongly** recommend analyzing the following collection examples for more context:

* [Polity](./examples/polity.json)
* [E-Waste](./examples/e-waste.json)

## Collection Schema

Data is delivered to the Eartheos app in [JSON](https://en.wikipedia.org/wiki/JSON) format.  Please adhere to this schema when constructing your data set to ensure the smoothest integration with Eartheos.

### Collection

A `Collection` is the Eartheos API's top-level object.  Each card on Eartheos's main view represents a collection.

```
{
	"metadata": CollectionMetadata, // required
	"style": LayerStyle,
	"layers": [Layer],
	"groups": [LayerGroup],
	"charts": [Chart],
	"key": [CategoryKeyItem | ValueKeyItem] // required
}
```

### CollectionMetadata

```
{
	"name": String, // required
	"author": String, // required
	"category": String, // required
	"sourceURLs": [String],
	"summary": String, // required
	"description": String, // required
	"links": [String],
	"cardImage": String // required	
}
```

### CategoryKeyItem

```
{
	"color": String, // required
	"icon": String, // required
	"title": String // required
}
```

### ValueKeyItem

```
{
	"colors": [String], // required
	"minTitle": String, // required
	"maxTitle": String // required
}
```

### Chart

```
{
	"type": "string", // required
	"data": { 
		String : [Number] 
	},  // required
	"colors": [String], // required
	"summary": String
}
```

### LayerGroup

```
{
	"metadata": LayerGroupMetadata,  // required
	"style": LayerStyle, 
	"layers": [Layer]  // required
}
```

### LayerGroupMetadata

```
{
	"name": String  // required
}
```

### Layer

```
{
	"metadata": LayerMetadata,  // required
	"style": LayerStyle,  // required
	"points": [LayerPoint],  // required (if polygons undefined)
	"polygons": [LayerPolygon]  // required (if points undefined)
}
```

### LayerStyle

```
{
	"color": String,  // required (if scale undefined)
	"scale": [String] // required (if color undefined)
}
```

### LayerMetadata

```
{
	"name": String, // required
	"unit": String
}
```

### LayerPoint

```
{
	"name": String, // required
	"text": String,
	"url": String,
	"value": Number,
	"key": String, // required (if lat/lon undefined)
	"lat": Number, // required (if key undefined)
	"lon": Number, // required (if key undefined)
	"style": LayerPointStyle
}
```

### LayerPointStyle

```
{
	"color": String,
	"icon": String
}
```

### LayerPolygon

```
{
	"name": String, // required
	"text": String,
	"url": String,
	"value": Number,
	"key": String, // required (if bounds undefined)
	"bounds": [[Float]], // required (if key undefined)
	"style": LayerPolygonStyle
}
```

### LayerPolygonStyle

```
{
	"color": String
}
```
