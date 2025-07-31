---
tags:
  - design
---
### typography

- **for paragraphs use font size between 15 and 25 pixels**
- **for large headings use font size 60px**
- **for small headings use font size 32 px**
- **if you use very large font (eg 90px) then make it thin**
- **use line spacing between texts from 120 to 150%**
- **each line should have 45 to 90 characters**

### colors
- use only one base color
- use one "draw attention color"(ctas ,colored headings etc)


### IMAGES

To achieve the text-on-image effects I showed you before, you can use CSS for your websites. Here is **example CSS code** for some of the effects. Please change it according to your needs.

  

**Overlay the image**

.darken {

background-image: linear-gradient(rgba(0, 0, 0, 0.5), rgba(0, 0, 0, 0.5)), url(YOUR IMAGE HERE);

}  

Example: [http://jsfiddle.net/drpak8vy/1/](http://jsfiddle.net/drpak8vy/1/)

  

**Put text in a box**

.text-box {

background-color: rgba(0, 0, 0, 0.5);

color: #fff;

display: inline;

padding: 10px;

}  

Example: [http://jsfiddle.net/qg83m36p/](http://jsfiddle.net/qg83m36p/)

**Floor fade**

.floor-fade {

background: linear-gradient(to bottom, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.6) ), url(YOUR IMAGE HERE);

}

Example: [http://jsfiddle.net/gRzPF/409/](http://jsfiddle.net/gRzPF/409/)

