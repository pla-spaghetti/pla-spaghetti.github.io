## difference
## 4th January 2021
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

