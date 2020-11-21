---
layout: default
title: Elm Sprite Editor
permalink: /projects/elm-sprite-editor/
excerpt: A sprite editor for Gameboy games, written for the web using Elm. Displays the GBZ80 hex codes for a sprite
img_src: /assets/projects/sprite-editor/gb-sprite-editor.png
img_alt: Creeper face on the gb sprite editor
order: 5
---

## Elm Sprite Editor

While working on some Gameboy assembly code I found that encoding sprite image data by hand was very time consuming so I created the gameboy sprite editor to help. The project is written for browsers using Elm and it features a continous integration and deployment pipeline.

Try it out <a href="https://www.guydunton.com/gb-sprite-editor">here</a>. Source code is available [here](https://www.github.com/gdunton/gb-sprite-editor)

### How It Works

Gameboy games are made up of 8x8 tiles with each pixel made from 2 bits.

This means that there are 4 possible colors for each pixel:

- 00 - White
- 01 - Light grey
- 10 - Dark grey
- 11 - Black

For example:

![A sprite from Pokemon Red]({{ "/assets/projects/sprite-editor/poke_sprite.png" }})

The above sprite from Pokemon Red is of a table made of 8 separate tiles. I have added a box around a single one of the tiles. The top row of this sprite is 7 black pixels followed by 1 white pixel.

<table>
    <tr>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="black"><span style="color:white;">11</span></td>
        <td bgcolor="white">00</td>
    </tr>
</table>

The hex is created from the least significant bits then the most significant bits on each row.

| Least significant bit | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 0 |
| Most significant bit | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 0 |

This means that the binary for both rows is: 1111,1110 => 15, 15 => $FE $FE

Another example is the 4th row down in this tile:

<table>
    <tr>
        <td bgcolor="#666666"><span style="color:white;">10</span></td>
        <td bgcolor="#999999"><span style="color:white;">01</span></td>
        <td bgcolor="#999999"><span style="color:white;">01</span></td>
        <td bgcolor="#666666"><span style="color:white;">10</span></td>
        <td bgcolor="#666666"><span style="color:white;">10</span></td>
        <td bgcolor="#999999"><span style="color:white;">01</span></td>
        <td bgcolor="#999999"><span style="color:white;">01</span></td>
        <td bgcolor="#999999"><span style="color:white;">01</span></td>
    </tr>
</table>

or

| Least significant bit | 0 | 1 | 1 | 0 | 0 | 1 | 1 | 1 |
| Most significant bit | 1 | 0 | 0 | 1 | 1 | 0 | 0 | 0 |

<br/>

| Binary    | Decimal | Hex  |
| --------- | ------- | ---- |
| 0110,0111 | 6,7     | \$67 |
| 1001,1000 | 9,8     | \$98 |

The hexidecimal required for this row in GBZ80 is: $67, $98

### Writing in Elm

I chose to write the sprite editor in Elm because I wanted to be able to use it in the browser and for [various reasons](http://www.jsfuck.com/) I'm not really a fan of Javascript. Elm is a functional programming language with a great emphasis on ease of use and safety. Because it compiles to javascript I don't need a server to run it (handy because my site is hosted on github). For more information on Elm see [here](http://elm-lang.org/).

The process of writing the code was extremely fast. The process shown above is very easy to sketch out with Elm type signatures with the most awkward part being the conversion between binary and hex (Elm doesn't have any build in hex support).

### The Result

The part that I enjoyed the most about this project was setting up the continuous integration/deployment pipeline. Whenever code is commited into a branch; a travis server checks out the code compiles it and runs unit tests on it. Even better, following code being merged into master, travis again builds the code and commits it to this website! It's satisfying to make a change to the code and within minutes see it reflected in the live site.

There are more things that I've got planned to do in future with this project and I would like it to be easier to support a full level editor experiance.
