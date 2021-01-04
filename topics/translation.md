## translation
## 4th January 2021
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

Union is the opposite of difference it lets us add shapes together.

