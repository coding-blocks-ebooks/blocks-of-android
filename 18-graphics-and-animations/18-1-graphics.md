# **Graphics**

Graphics are what make your application visually appealing to users. We have various ways of adding graphics to our applications. 

We can add image graphics and use the ImageView to show these images.   
Another method is to make custom drawables in an XML file. The most complicated method is to handle the drawing process our self by using the canvas class.

We will choose between these options depending on the need of the application.   
For example, in case our application doesn’t require very complex graphics with fast animations, we should avoid using the third method.

The various methods we can use to display graphics in our app are : 

1. **Using images:  **We can add the graphic images to the drawable folder and set an ImageView’s src to be that resource. This is the simplest way of adding graphics and we should it whenever possible.
   This is something that all of us should be familiar with by now.
2. **Using XML: **We can create graphics using XML code as well. Before we jump into this, we should know what **drawables** are. 

### **Drawables: **

Drawables in Android are “Anything that can be drawn”.   
We have different kind of drawables like **ShapeDrawables**, **BitmapDrawable**, etc.

We can create a lot of interesting and complex graphics using these. Let’s look at various categories of XML resources that we can use.

### **Shape Drawables: **

We can make shapes like rectangle, oval, line etc using these drawables.   
Here is an example code to create a rectangle:

\`\`\`

```
<shape xmlns:android= http://schemas.android.com/apk/res/android android:shape="rectangle">
    <corners android:radius="0dp"/>
    <solid android:color="@android:color/white"/>
    <stroke android:width="1dp"
    android:color="@android:color/darker_gray"/>
</shape>
```

Now we can either use this shape inside another XML by referring to it as `@drawable/rectangle` or we can access it inside code by using `getResources().getDrawable(R.drawable.rectangle)`

### **State List:**

A StateListDrawable is used to represent a view, which requires different views based on the state it is in. e.g. Buttons might need different view depending on whether they are selectable or  not. We will use the selector tag for StateListDrawable. Here is an example code:

```
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/oval" android:state_pressed="true"/>
    <item android:drawable="@drawable/rectangle"/>
</selector>
```

Now we can use this drawable as background of some other element and when that element is clicked the background will use the oval.xml drawable. Otherwise rectangle.xml will be used.

### **Layer list:**

LayerDrawable is used when you want layers of other drawables in your view.   
Items in the list are drawn in the order in which we list them; first item in the list is drawn first, very similar to a FrameLayout. Here is an example code:

```
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
<item android:drawable="@drawable/rectangle" android:left="40dp"/>
<item android:drawable="@drawable/oval" android:right="40dp"/>
</layer-list>
```

This will make two layers one corresponding to the rectangle.xml and the other for oval.xml.

### **Level List:**

LevelListDrawable is used when you have multiple drawables corresponding to a view.   
Each drawable in this list is mentioned along with a level. This can be used as source for an image view and the drawable will be shown depending on the level that you set using setLevel/setImageLevel function on the image view. The first list in the item with level greater or equal to the level set will be used. 

```
<level-list xmlns:android="http://schemas.android.com/apk/res/android">
<item android:maxLevel="0" android:drawable="@drawable/oval"/>
<item android:maxLevel="1" android:drawable="@drawable/ic_launcher"/>
</level-list>
```

### **Transition:**

TransitionDrawable is a subclass of LayerDrawable. This supports only two layers of drawables. This drawable will show the first  one by default but you can fade in/out the second one using startTransition/ reverseTransition function. You can also use the reset Transition function. 



```
<?xmlversion="1.0"encoding="utf-8"?>
<transition xmlns:android="http://schemas.android.com/apk/res/android">
<item android:drawable="@drawable/ic_launcher"/>
<item android:drawable="@drawable/oval"/>
</transition>
```

Here is how we can start the transition in your Java code.

```
ImageView iv = (ImageView)findViewById(R.id.imageView1);
TransitionDrawable drawable = (TransitionDrawable) iv.getDrawable();
drawable.startTransition(1000);
```

We can also use `setCrossFadeEnabled()` to hide first view, when second one becomes visible.

### **Clip:**

ClipDrawable shows a part of child drawable depending on the current level.   
For example, we can put an image inside the Clip drawable and make it as source of an ImageView.   
We can change the level of clip drawable to show/hide the image. Here is an example:

```
<clip xmlns:android="http://schemas.android.com/apk/res/android" 
    android:drawable="@drawable/ic_launcher"
    android:clipOrientation="vertical"
    android:gravity="top"/>
```

Clip orientation tells us if the image will be shown horizontally or vertically.

Here is how we can change the level of clip drawable.

```
ImageView iv = (ImageView)findViewById(R.id.imageView1);
ClipDrawable drawable = (ClipDrawable)iv.getDrawable();
drawable.setLevel(level);
```

### **Inset:**

Inset drawables are used when we want to put a smaller drawable inside another drawable. For example, we might want to put a smaller drawable as background of a larger drawable. 

### **Custom View Class:**

We can create our own TextView, RelativeLayout etc by making a subclass of them.   
We would do this generally to add helper functions, which will let us keep other classes clean by moving code related to that view inside this subclass. 

For example, we might want to do some work every time a view is initialized, in this case we might want to create a subclass and overwrite the constructor and call the initialization code from the constructor. 

