# OpenSCAD - T18 potentiometer/encoder shaft
## 4th January 2021

The goal is to make a model for this type of shaft.

![T18 encoder](https://cdn.discordapp.com/attachments/794700507448475679/795680873977479188/Screenshot_2021-01-04_at_15.40.18.png)

Or is that a 20 tooth? There are different components that look like this but have slightly different specs. We best make a model that takes parameters for these. Diameter, number of teeth, slotted, height, should all be parameters.

![T18 diagram](https://cdn.discordapp.com/attachments/794700507448475679/795682374317899796/Screenshot_2021-01-04_at_15.42.51.png)

This is the diagram for the T18 shaft. [[1]](https://www.thonk.co.uk/wp-content/uploads/2019/11/Alpha-RD901F-20-15K-B10K-0018.pdf)

![EC11 diagram](https://cdn.discordapp.com/attachments/794700507448475679/795683342438629476/Screenshot_2021-01-04_at_16.00.33.png)

This is the diagram from an EC11 shaft. [[2]](https://tech.alpsalpine.com/prod/e/html/encoder/incremental/ec11/ec11e1834403.html)

A good starting point would be a stack of two cylinders, one being the standoff, the other being the knurled part.
```scad
$fn=50;
module knurled_shaft(
    standoff_radius = 7
  , standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  ) {
  cylinder(r=standoff_radius,h=standoff_height);
  translate([0,0,standoff_height])
      cylinder(r=knurled_radius,h=knurled_height);
}
knurled_shaft();
```

![cylinder stack](https://cdn.discordapp.com/attachments/794700507448475679/795686044841803826/Screenshot_2021-01-04_at_16.11.02.png)

Let's work on some teeth as a separate module. We want to generate a shape we can subtract from the knurled part to make teeth. This is a good starting point and is almost what we want. We just need a few more parameters to control the tooth shape.
```scad
module teeth(
    standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  ,num_teeth = 20
) {
    for(tooth = [0:num_teeth] ) {
        rotate([0,0,tooth*(360/num_teeth)]) translate([knurled_radius,0,standoff_height])
          cube([1,1,knurled_height]);
    }
}
#teeth();
```

![almost teeth](https://cdn.discordapp.com/attachments/794700507448475679/795689481579397170/Screenshot_2021-01-04_at_16.22.20.png)

Teeth are generated by looping over a variable *tooth* from 0 to (num_teeth - 1).  And generating a shape for each tooth based on its *tooth* index.

The tooth shape is a cube that is 1x1xknurled_height that has been translated out to knurled_radius on the x axis and up to standoff_height in the z-axis. Each tooth is then rotated by tooth*(360/num_teeth).

The teeth are rendered with the debug *#* modifier which makes them red in the debug view.

Adding a 45 degree rotation to the cube gets us closer to the desired result.

![45 rotation](https://cdn.discordapp.com/attachments/794700507448475679/795692741170429992/Screenshot_2021-01-04_at_16.38.01.png)

The cuboids are now overlapping the shaft.

```scad

module teeth(
    standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  ,num_teeth = 20
) {
    for(tooth = [0:num_teeth] ) {
        rotate([0,0,tooth*(360/num_teeth)]) 
          translate([knurled_radius,0,standoff_height])
            rotate([0,0,45])
               cube([1,1,knurled_height]);
    }
}
#teeth();

```

In order to get the teeth to be evenly spaced around the shaft we need to employ some Pythagoras. We create the cuboid with sides of a size that give a hypotenuse that is an equal part of the knurled shafts circumference. The cuboid is then rotated 45 degrees and centered on the origin before being moved into place.

```scad

module teeth(
    standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  ,num_teeth = 20
) {
    circumference_part = 2*PI*knurled_radius/num_teeth;
    square_edge = sqrt(pow(circumference_part,2)/2);
    for(tooth = [0:num_teeth] ) {
        rotate([0,0,tooth*(360/num_teeth)])
        translate([knurled_radius,0,standoff_height])
        translate([0,-circumference_part/2,0])
        rotate([0,0,45]) 
        cube([square_edge,square_edge,knurled_height]);
    }
}
#teeth();
```

![squares in right place](https://cdn.discordapp.com/attachments/794700507448475679/795695992623333386/Screenshot_2021-01-04_at_16.51.17.png)

Let's rename our knurled_shaft module to basic_shaft so that knurled shaft can become the difference of the basic shaft and the teeth.

```scad
$fn=50;
module basic_shaft(
    standoff_radius = 7
  , standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  ) {
  cylinder(r=standoff_radius,h=standoff_height);
  translate([0,0,standoff_height])
      cylinder(r=knurled_radius,h=knurled_height);
}

module teeth(
    standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  , num_teeth = 20
) {
    circumference_part = 2*PI*knurled_radius/num_teeth;
    square_edge = sqrt(pow(circumference_part,2)/2);
    for(tooth = [0:num_teeth] ) {
        rotate([0,0,tooth*(360/num_teeth)])
        translate([knurled_radius,0,standoff_height])
        translate([0,-circumference_part/2,0])
        rotate([0,0,45]) cube([square_edge,square_edge,knurled_height]);
    }
}

module knurled_shaft(

    standoff_radius = 7
  , standoff_height = 16
  , knurled_radius = 6
  , knurled_height = 11
  , num_teeth = 20
  ) {
  difference() {
      basic_shaft(
        standoff_radius = standoff_radius
      , standoff_height = standoff_height
      , knurled_radius = knurled_radius
      , knurled_height = knurled_height
      );
      #teeth(
        standoff_height = standoff_height
      , knurled_radius = knurled_radius
      , knurled_height = knurled_height
      , num_teeth = num_teeth
      );
  }      
}
knurled_shaft();
```

![debug shaft render](https://cdn.discordapp.com/attachments/794700507448475679/795698011211825182/Screenshot_2021-01-04_at_16.57.20.png)

The preview render *F5* looks like this because of the # modifier before teeth in the knurled_shaft module.

![finished product](https://cdn.discordapp.com/attachments/794700507448475679/795698252116131871/Screenshot_2021-01-04_at_16.57.44.png)

The full render *F6* is an approximate model for the type of shaft we are interested in. Hopefully it will be good enough to subtract a void for a part that fits on the shaft.
