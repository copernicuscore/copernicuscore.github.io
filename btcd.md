
## Thanks btcd's work
In the course of our development Copernicus , We are particularly grateful for the efforts and contributions of btcd in the development of Bitcoin protocol . Their work so that we use golang development Bitcoin will be easier . 
In some places in Copernicus directly use the btcd code , here to explain the next to express our respect for btcd . We are to do some changes in some places , so thar this code  is suitable for our ideas , for this part of the code we will the license and the copyright of btcd . 

If there are missing , please let us know and we will make up as soon as possible.
Below is a brief description of the code used and the code modified .

[btcutil](https://github.com/btcsuite/btcutil) , We used the mudule directly in our code , At first we maked it as a library , But we still copy part of code in btcutil , the reason is that btcutil has some code that depends on btcd . For example , it will depends on :`btcd/txscript` and `btcd/wire` , and these module we will be re-planning . So we remove tx , block and bloom  from btcutil , retained the rest of the code.

[btcec](github.com/btcsuite/btcd/btcec) , We directly used  the `btcec` module in Copernicus , we are used to operate ECDSA . Later we used the library [secp256k1-go](https://github.com/btccom/secp256k1-go) , this library uses CGO to encapsulate secp256k1(c language) .  But we still retain the `btcec` meaningful init test.

In the read and write section of the Bitcoin protocol , we reger to a large number of btcd codes . This part of work btcd done very well , they have also been a log of use and testing . We are very sincere thanks to the work of btcd , their contribution to the whole bitcoin community .  So do this part of the development work when we are based on  btcd's code . This section is used in three mudules:
1. `utils` : Common data structures used to handle protocols , such as Varint , VarString , and base type read and write
2. `msg`: The module is used to deal with the various information in the special Bitcoin protocol , here we alse refer to a lot of btcd code .
3. `peer`: used to manage the node and the node communicaion ,  we also refer to  btcd's code

Of course , there are other parts we will refer to btcd's code , in the future we will also reger to btcd code , we will make a brief description the part that is used directly , to express our thanks to btcd . With the efforts of the btcsuite team , it would be easier for other developers to develop Bitcoin teams using Go.

Thanks to  the btcsuite team and the developers who contributed to `btcd` and `btcutil` .

Contributors of `btcutil` :
adiabat,    waldyrious,    Roasbeef,    cpacia,    benma,    afk11,    jcvernaleo,    flammit,    wallclockbuilder,    dajohi,    jrick,    davecgh

Contributors of `btcd`:

justusranvier,    ortutay,    esemplastic,    dskloet,    drazisil,    yrashk,    waldyrious,    runeaune,    rvelhote,    oleganza,    IngCr3at1on,    fanquake,    mdempsky,    Kefkius,    jadeblaquiere,    jzbz,    ysangkok,    hectorj,    federicobond,    evansuva,    mvdan,    shphrd,    turboroot,    beldur,    lologarithm,    benhc123,    carryforward,    bclermont,    cpacia,    mischief,    verokarhu,    chris-martin,    gsalgado,    ishbir,    halseth,    marcopeereboom,    dan-da,    DanielKrawisz,    alexbosworth,    toddfries,    tb00,    dskloetg,    kargakis,    Dirbaio,    wallclockbuilder,    tsenart,    stevenroose,    jimmysong,    aakselrod,    owainga,    jongillham,    flammit,    tuxcanfly,    drahn,    Roasbeef,    jcvernaleo,    jrick,    dajohi,    davecgh    

