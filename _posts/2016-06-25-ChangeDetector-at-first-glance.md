
# [ChangeDetector Demo CODE](http://plnkr.co/edit/iV8RTftEqbGiIcI4331W)

## Interface
```javascript
class ChangeDetectorRef {
//Marks all ChangeDetectionStrategy ancestors as to be checked.
	markForCheck() : void
//Detaches the change detector from the change detector tree.
//The detached change detector will not be checked until it is reattached. 
	detach() : void
//Checks the change detector and its children.
	detectChanges() : void
//Checks the change detector and its children, and throws if any changes are detected.
//This is used in development mode to verify that running change detection doesn't introduce other changes.
	checkNoChanges() : void
//Reattach the change detector to the change detector tree.
//This also marks OnPush ancestors as to be checked. This reattached change detector will be checked during the next change detection run.
	reattach() : void
} 


class ChangeDetectionStrategy {
//CheckedOnce means that after calling detectChanges the mode of the change detector will become Checked.
	CheckOnce 
// means that the change detector should be skipped until its mode changes to CheckOnce
	Checked 
	//means that after calling detectChanges the mode of the change detector will remain CheckAlways.
	CheckAlways 
//means that the change detector sub tree is not a part of the main tree and should be skipped.
	Detached 
//it will only trigger change detection when an input property has been changed( in parent )as the result of a change detection check.
//in another words,the component with this strategy won't check changes proactively by itself unless the parent push it to do so when parent knows that the input property for child is changed(reference change).  can Use immutable object to capture the all kind of changes.
//refer to http://plnkr.co/edit/5Xm11FIwAaQcLPeEdFdx?p=preview
	OnPush 
	//value is CheckAlways
	Default 
}
```

## some combination/usage scenario

#### **ChangeDetectionStrategy.CheckOnce/markForCheck**

Imagine the ***data changes constantly, many times per second***. For performance reasons, we want to check and update the list every  seconds.

[view sample](http://plnkr.co/edit/4PB1tR?p=preview)
 
#### **detach/reattach**

design as a switch,for some reason don't want the change to relefct in the page then you can `detach`, while the change is continuosly undergoing. 

```javascript
 @Input()
  set live(live:boolean){
    if(live)
      this.ref.reattach();
    else 
       this.ref.detach();
  }
```

#### **ChangeDetectionStrategy.OnPush**
[view sample](http://embed.plnkr.co/F8zVKr/)
