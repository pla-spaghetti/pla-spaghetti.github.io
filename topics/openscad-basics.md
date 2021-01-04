# OpenSCAD basics
## 4th January 2021

## cubes
A 1x1x1 cube
```scad
cube(1);
```

![1x1x1 cube](https://cdn.discordapp.com/attachments/794700507448475679/795468352503414804/Screenshot_2021-01-04_at_01.45.07.png)

The cube primitive behaves like you would expect given a single size argument by generating a cube of that size. It can however take a vector argument to specify lengths of sides in [x,y,z]. So cube also lets us make cuboids.
```scad
cube([1,2,3]);
```

![[1,2,3] cuboid](https://cdn.discordapp.com/attachments/794700507448475679/795469528377196605/Screenshot_2021-01-04_at_01.51.00.png)

## translation
Translation is one of the fundamental operations we can use to place shapes in space. The translate operation takes an [x,y,z] style argument vector. It moves all child objects placed inside it along this vector.
```scad
translate([1,0,0]) cube([1,2,3]);
```
This translates our cuboid by 1 in the x direction.

![[1,2,3] cuboid translated [1,0,0]](https://cdn.discordapp.com/attachments/794700507448475679/795471038410260510/Screenshot_2021-01-04_at_01.53.17.png)

## rotation
Rotation is another fundamental when it comes to moving shapes. The rotate operation also takes an [x,y,z] vector. The values are in degrees and the rotation is a "right-hand rule" behaviour about the corresponding axis.
```scad
rotate([90,0,0]) cube([1,2,3]);
```

![cuboid rotate 90 x](https://cdn.discordapp.com/attachments/794700507448475679/795473800346206218/Screenshot_2021-01-04_at_02.08.20.png)

## difference
Subtracting one object from another is a great way to make a more complex shape. We can take our cube from our cuboid like this.
```scad
difference() {
    cube([1,2,3]);
    cube(1);
}
```

![cube taken from cuboid](https://cdn.discordapp.com/attachments/794700507448475679/795475171460382740/Screenshot_2021-01-04_at_02.11.42.png)

## union

Union is the dual of difference. It lets us add shapes together.

```scad
union() {
    cube([1,2,3]);
    cube([2,1,1]);
}
```

![union of cuboids](https://cdn.discordapp.com/attachments/794700507448475679/795481342040080394/Screenshot_2021-01-04_at_02.37.53.png)

Union is so fundamental an operation it is the default thing that happens in your scad program. This union of shapes can be expressed equivalently and more concisely like so.
```scad
cube([1,2,3]);
cube([2,1,1]);
```
This is just what sequencing operations in openSCAD is - a union of shapes. So why do we need a union operation at all when scad just takes care of that for us?

If we want to take away the unit cube from this union of shapes we might try this.
```scad
difference() {
    cube([1,2,3]);
    cube([2,1,1]);
    cube(1);
}
```
![cube taken from cuboid](https://cdn.discordapp.com/attachments/794700507448475679/795475171460382740/Screenshot_2021-01-04_at_02.11.42.png)

Which is perfectly valid but does not do what we want. The difference operation is removing all of the subsequent children from the first child expression so this looks just like one of our earlier more boring shapes. To achieve the desired result we need the first child to be the union.
```scad
difference() {
    union() {
      cube([1,2,3]);
      cube([2,1,1]);
    }
    cube(1);
}
```
Now we have what we want.

![cube from union](https://cdn.discordapp.com/attachments/794700507448475679/795484163460694046/Screenshot_2021-01-04_at_02.49.35.png)

## sphere
Cubes have their limitations. Let's introduce the sphere.
```scad
sphere(r=1);
```

![non sphere](https://cdn.discordapp.com/attachments/794700507448475679/795486231126605844/Screenshot_2021-01-04_at_02.57.54.png)

That's not very spherical looking is it? To fix that we have to introduce *the special variables*. These are $fa, $fs, and $fn. These allow you to set *the number of facets used to generate an arc* . This lets you trade rendering performance for model detail. For now this fixes our problem by increasing the number of *fragments*. But this approach may not be the best if your model is complicated.
```scad
$fn=100;
sphere(r=1);
```

![spherical sphere](https://cdn.discordapp.com/attachments/794700507448475679/795488374106357770/Screenshot_2021-01-04_at_03.00.42.png)

## cylinders
As we have seen, spheres can be *unpredictable*... Cylinders are where it's at. And with some rotation, translation, and difference we can make something truly magnificent.
```scad
$fn=50; //100 took too long to render so it's 50 now
difference() {
    cylinder(r=1,h=4);
    cylinder(r=0.8,h=4);
    translate([0,0,2]) rotate([90,0,0]) cylinder(r=0.4,h=4);
}
```

![magnificent](https://cdn.discordapp.com/attachments/794700507448475679/795490843674279966/Screenshot_2021-01-04_at_03.16.16.png)

Cylinder, like cube, has some tricks up its sleeve. Instead of just specifying a single *r*, the radius of the circles making up both ends of the cylinder, we can specify *r1* and *r2*, which opens up a world of cone shaped possibilities.
```scad
$fn=50;
difference() {
    cylinder(r1=1,r2=0.8,h=4);
    cylinder(r=0.8,h=4);
    translate([0,0,2]) rotate([90,0,0]) cylinder(r=0.4,h=4);
}
```

![by the cones of dunshire](https://cdn.discordapp.com/attachments/794700507448475679/795491785023029268/Screenshot_2021-01-04_at_03.19.59.png)

## modules
Now we have made the perfect object we might want to give it a name to call it by. Then we won't have to copy and paste its code whenever we want to make one.
```scad
$fn=50;
module holey_cone() {
    difference() {
        cylinder(r1=1,r2=0.8,h=4);
        cylinder(r=0.8,h=4);
        translate([0,0,2]) rotate([90,0,0]) cylinder(r=0.4,h=4);
    }
}
holey_cone();
translate([3,0,0]) holey_cone();
```

![two towers](https://cdn.discordapp.com/attachments/794700507448475679/795493459670466590/Screenshot_2021-01-04_at_03.26.33.png)

Modules can do more than just give our objects names. They also accept arguments which lets us parameterise them. We can allow change of scale of our holey_cone like this.
```scad
$fn=50;
module holey_cone(scale) {
    difference() {
        cylinder(r1=1*scale,r2=0.8*scale,h=4*scale);
        cylinder(r=0.8*scale,h=4*scale);
        translate([0,0,2*scale]) rotate([90,0,0]) cylinder(r=0.4*scale,h=4*scale);
    }
}
holey_cone(1);
translate([3,0,0]) holey_cone(1.4);
```

![scaled cone](https://cdn.discordapp.com/attachments/794700507448475679/795494309260296252/Screenshot_2021-01-04_at_03.29.57.png)

## for loops
A for loop can be used to create ensembles of shapes. The loop index can be used to set parameters for each shape since it is available as a variable within the loop braces.
```scad
for(s = [0,1,2,3,4]) {
  translate([s*4 + s,0,0]) holey_cone(1 + s*0.4);
}
```

![family of cones](https://cdn.discordapp.com/attachments/794700507448475679/795495976532967444/Screenshot_2021-01-04_at_03.34.10.png)

That's enough basics for now. [If you want to know more the OpenSCAD user manual can probably answer your questions.](https://en.wikibooks.org/wiki/OpenSCAD_User_Manual/The_OpenSCAD_Language#Variables) Till next time!

## intersection
Another basic transformation we may wish to perform on shapes is an intersection. The space where two (or more) shapes overlap might also be an interesting shape.
```scad
intersection() {
    sphere(1);
    cube(3);
}
```

![eighth of a sphere](https://cdn.discordapp.com/attachments/794700507448475679/795670858353213530/Screenshot_2021-01-04_at_15.09.13.png)

If we want to see the individual shapes that created this composite we can modify the code or comment parts out to get a look but this will change our model. By using  a *modifier character* to render parts of the model differently we can change the preview model (*F5*) but still render the full model as normal (*F6*). The *#* symbol will render all of its child objects in a different colour and opacity in the preview mode only.
```scad
intersection() {
    sphere(1);
    #cube(2);
}
```

![debug render](https://cdn.discordapp.com/attachments/794700507448475679/795674281547530270/Screenshot_2021-01-04_at_15.25.09.png)

[For more information on modifiers see OpenSCAD manual page on debugging aids.](https://en.wikibooks.org/wiki/OpenSCAD_User_Manual/Modifier_Characters)
