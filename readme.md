![alt tag](./assets/eartheos-dev_docs_github_banner.png)
# Eartheos for Developers

Our mission is to give users perspective on global issues by transforming the Earth into a storytelling and educational platform. Developers, institutions, and students alike use the Eartheos app to visualize a topic as it relates to different locations around the world. With the globe at the center of our experience, we seek to transport users to the international space station where they can explore our planet through layers of information.

## Testing Data

### Hosting your JSON

To test the way your data renders in the Eartheos app, you can host your JSON file somewhere on the public internet (Dropbox, Amazon S3, etc) and use this URL for testing.

If you prefer, you can also set up a web server on your local machine to serve your JSON files. If you don't have one handy, we recommend the [http-server](https://github.com/indexzero/http-server) package. When doing this, ensure that you run the server from the directory in which your JSON file(s) reside. 

When entering the url into the Eartheos app, use your local area network IP address. It'll most likely look like `192.168.x.x`, `10.x.x.x`, or `172.x.x.x`.  Local addresses like `127.0.0.1` or `localhost` will not work.

### Loading the data

Once you have your data hosted (either on a server you set up or elsewhere), open Eartheos on your iOS device and scroll to the "Add Your Data" card.  Tap "Learn More" and follow the on-screen instructions.

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
	"key": [CategoryKeyItem | ValueKeyItem], // required
	"mapType": String
}
```

###### Layers

`Layer`s included in the `layers` array of the `Collection` are always visible when the collection is on the globe, regardless of the globe's state or which `LayerGroup` is selected. 

###### Groups

Layers included in a `LayerGroup` under a `Collection`'s array of `groups` are only visible when that specific `LayerGroup` is selected. This is particularly useful when displaying time-series data. The usual practice is to create `LayerGroup`s with their `LayerGroupMetadata` `name` attributes set to the year the enclosed `Layers` represent. After doing that, the user will be able to compare them by swiping on the picker.

###### Keys

The `Collection`'s `key` array allows you to define the keys/legends that appear in the cards at the bottom of the globe screen. See the `CategoryKeyItem` and `ValueKeyItem` schemas for more information.

###### Style

The `style` `Collection` allows for the definition of a default "base" layer style at the collection level. `LayerStyle`s set on individual layers will override this setting if they are specified.

###### Map Types

The `mapType` field is optional and defaults to `satelliteHybrid`. Alternatively, it can be set to one of the following:

| Identifier    | Description   | Preview |
| :-----------: |:-------------:| :----:  |
| `satellite` | MapBox satellite tiles | ![alt tag](./assets/tiles-satellite.jpg) |
| `satelliteHybrid` | MapBox satellite tiles with administrative boundaries / streets / country names / etc. | ![alt tag](./assets/tiles-satelliteHybrid.jpg)
| `light` | MapBox map tiles with light color scheme | ![alt tag](./assets/tiles-light.jpg) |
| `dark` | MapBox map tiles with dark color scheme | ![alt tag](./assets/tiles-dark.jpg)
| `correctedReflectance` | NASA "corrected reflectance" tileset | ![alt tag](./assets/tiles-correctedReflectance.jpg) |
| `night` | NASA "Earth at night" tileset | ![alt tag](./assets/tiles-night.jpg) |

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

`CategoryKeyItem` objects are used to instruct Eartheos to render a key items. 

```
{
	"color": String, // required
	"icon": String, // required
	"title": String // required
}
```

For example, the following `CategoryKeyItem ` renders the following icon/color combination:

```
{
    "color": "#FC9723",
    "icon": "fa-amazon",
    "title": "Amazon Data Center"
}

```

![alt tag](./assets/category-key.png)

### ValueKeyItem

`ValueKeyItem` objects are used to instruct Eartheos to render a key item that reflects a gradient scale of values. 

```
{
	"colors": [String], // required
	"minTitle": String, // required
	"maxTitle": String // required
}
```

For example, the following `ValueKeyItem` renders the following scale:

```
{
	"colors": [
		"#0286EF",
		"#CEF3FF"
	],
	"maxTitle": "Democratic",
	"minTitle": "Autocratic"
}
```
![alt tag](./assets/gradient-key.png)


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

Currently, there are two supported `Chart` types:

| Type    | Notes   | Preview |
| :-----------: |:-------------:| :----:  |
| `bar` | Currently, no limit exists on the number of categories you can represent under the `data` field. Enjoy `bar` responsibly. | ![alt tag](./assets/graph-bar.png) |
| `pie` | Automatically calculates percentages based on the data passed. Only pass one number in each array under the `data` field. | ![alt tag](./assets/graph-pie.png) |

These charts appear in the "more info" section of your dataset.

## Visualisations

Currently there are four supported visualisation types:

| Type    | Notes   | Preview |
| :-----------: |:-------------:| :----:  |
| `region` | These are regions on the map that have a `color`(red, blue, yellow). They can also be considered categorized regions. | ![example region](./assets/region.PNG) |
| `point` | These are markers on the map. Their appearance is controlled with a `color` and an `icon`. | ![example point](./assets/points.PNG) |
| `region + value` | This is a `region` accompanied by a `value`. The color of each region is determined by the `value` and the `scale`. | ![example region with value](./assets/region_value.PNG) |
| `point + value` | This is a `point` accompanied by a `value`. This becomes a cylinder on the globe whose height represents its `value`. They can have any `color`. | ![example point with value](./assets/point_value.PNG) |

There are two ways we handle data that overlaps on the globe:

| Type    | Notes   | Preview |
| :-----------: |:-------------:| :----:  |
| `overlapping regions` | When regions from two collections overlap one will lose its color and each `region`'s `value` will instead be represented by a pattern.  | ![example of overlapping regions visualisation](./assets/overlapping_regions.PNG) |
| `overlapping points` | When two or more points are in the exact same location the markers will be offset for visibility but slightly overlapping.   | ![example of overlaping points](./assets/overlapping_points.PNG) |


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

**Note:** values for the `icon` field in this structure come from [FontAwesome](http://fontawesome.io/icons/) and are prefixed with `fa`. For example: 

```
{ 
	"icon": "fa-cubes" 
}
```

will render [this](http://fontawesome.io/icon/cubes/) icon.

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
