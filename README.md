
**WBTMXTool** is a tool that makes it easy to scale TMX asstets between HD and SD.

Please visit the original post [here](https://www.wasabibit.com/wbtmxtool/).

### References - Who is using this tool (As of Nov 2017):
- Websites referencing WBTMXTool:
	- [WizardFu Games](https://wizardfu.com/book/cocos2d-x/tilemaps-box2d/)
	- [bjorn's github](https://github.com/bjorn/tiled/wiki)
- Books describing WBTMXTool:
	- [王者歸來：Cocos2D權威指南：用Cocos2D 超高效開發商業版iOS遊戲](http://global.pchome.com.tw/prod/DJAA2V-A90051DZH) (It's interesting to see it mentioned in Chinese although I don't read Chinese.)
	- [『繁體書』Cocos2D權威指南：用Cocos2D 超高效開發商業版iOS遊戲](http://ww.megbook.com.hk/mall/detail2.jsp?proID=2599168#.WgDQFyMrKfU)
- More threads:
	- [A Stackoverflow thread](https://stackoverflow.com/questions/32077337/scaling-assets-without-changing-sprite-position)


### A Story - Background

[Note: Original post written back in 2010]

Recently there has been a big enhancement to cocos2d-iphone code for the Retina Display support. This has made it a lot easier for iPhone developers to support HD and SD graphics in their games. Big Thank You to Riq for this!

When I started supporting HD a few days ago, I have found that there are still some issues when we are using both HD and SD TMX files in the game. Basically I had hoped that cocos2d would return Points by default no matter what format I use based on -hd suffix in the TMX file. The RetinaDisplay wiki page currently indicates that the complex TMX object uses Pixels, not Points. My test has also proved that this is true and that the returned tile object’s coordinate systems are not converted to Points at all. I guess this falls into the 5% exception of the current cocos2d-iphone’s RetinaDisplay support.

There can be several options to work around this issue. One option may be to use a single TMX file for coordinates of objects across HD and SD graphics and to convert coordinates in run time. With this option, when objects are included in the same TMX file along with layers for graphics, they will not be in sync in terms of locations of objects in either one of the HD/SD formats.

To support HD and SD together for my upcoming games going forward, it is my preference that I start designing everything in HD - i.e. HD graphics, HD coordinates in TMX. When I am ready to support SD at a point, I want to use tools for the HD-to-SD conversion - no manual work again! It’s also ideal to have a command-line tool so that it can be easily integrated with my automatic X-Code build process. Even though I can use the commercial tools such as Zwoptex or TexturePacker to generate SD images, I had no luck to finding a tool for TMX files to meet the needs.

I think my development process may be unique but hope that some of you may also find this article useful when you are developing.

This is a brief background of why I made this tool/prototype.

### How to use WBTMXTool

After many many hours, I have come up with a prototype - two step process.

Basically this tool converts design-time HD/RetinaDisplay-pixel based TMX files to corresponding SD files with SD pixels and vice versa.

Convert and prepare HD tmx files to SD by using a new tool WBTMXTool I just created. This will convert all HD pixels to SD pixels (or vice versa if necessary).

- Syntax

```
Syntax: $WBTMXTool -in <input_file_with_or_w/o_path> -out <output_file_with_or_w/o_path> [-scale <0.5|2.0|any_number>] [-suffixForHD <-hd|any> -suffixAction <none|add|remove>] [ -xmlVersion <1.0|any> -xmlEncoding <UTF-8|any>] [-outCondensed <yes|no>]
```
- The scale option **‘-scale’** applies to the following **tag:attribute** pairs.

```
object:x
object:y
object:width
object:height
map:tilewidth
map:tileheight
map:spacing
map:margin
tileset:tilewidth
tileset:tileheight
tileset:spacing
tileset:margin
(Added two more below on 9/28/2011. Thanks to rgbedin for finding the issue!)
image:width
image:height
(Added the following on 1/8/2013 to uptake new tags in Tiled)
tileoffset:x
tileoffset:y
polyline:points
polygon:points
```

- The suffix option **‘-suffixAction’** applies to the following **tag:attribute** pairs.

```
image:source
```

Create a Category class ‘CCTMXXMLParser+parser.mm’ based on CCTMXMapInfo in CCTMXXMLParser.m to override parseXMLFile and parser. This will do conversion of all pixels to points by using CC_CONTENT_SCALE_FACTOR() in run-time.
My prototype above seems to work now both in non-Retina devices and Retina devices. My posting is already lengthy. I will shorten my posting here.

#### History of Changes
- ver. 1.0-RC3 (Released on 1/9/2013) 
	- Changes: support polygon
- ver. 1.0-RC2 (Released on 1/8/2013)
	- Changes: support polyline and tileoffset
- ver. 1.0-RC1 (Released on 9/28/2011)
- ver. 1.0-RC0 (Released on 12/8/2010)

### How to Run
There are two ways to run the tool:

- Running WBTMXTool from command line:

```
From SD to HD:
./WBTMXTool -in sample.tmx -out sample-hd.tmx -scale 2.0 -suffixForHD -hd -suffixAction add

FRom HD to SD:
./WBTMXTool -in sample-hd.tmx -out sample.tmx -scale 0.5 -suffixForHD -hd -suffixAction remove

```

- Running WBTMXTool as a preprocess before your main project:

>
Here in this example, this is the way I am currently using WBTMXTool for me to convert all HD files in a specified directory to SD files in a different directory. (If you want, you can do in the reverse way - from SD to HD.)
> 1. Place the executable in ${SRCROOT}/../WBTMXTool directory.
WBTMXTool executable is within WBTMXTool directory.
>2. Create staging directories:
>${SRCROOT}/../StageTMXs/eachOne-hd <- For HD TMX files (I create HD TMX files only and put all of them here.)
>${SRCROOT}/../StageTMXs/eachOne <- For SD TMX files (Empty directory initially)
>3. Place all your HD TMX files in ${SRCROOT}/../StageTMXs/eachOne-hd
>4. Don’t forget to make your game project dependent on automate_stuff.sh in Xcode.

Please note that the source TMX files in this example start with TileMap and TexturePacker is used for scaling the tile images themselves separately.

It’s possible that this script may not work in your project most likely due to the directory structures. If this happened, please check if each of the directories used here is correctly found and reached in the script and adjust the script accordingly.

Thanks,

WasabiBit
