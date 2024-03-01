# Set or modify display resolution by command
This how to is to modify display resolusion on kvm terminal, because we found several case\
cli display was too small or too big


#check list monitor 
```
xrandr --listactiveresolutions
```

#check mode yg tersedia
```
xrandr 
```

#execute atau set monitor VGA-1 ke mode resolution
```
xrandr --output VGA-1 --mode 800x600
```
