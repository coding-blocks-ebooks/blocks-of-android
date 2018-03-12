# **Animations:**

Users must have a great experience with your application, to make your app stand out among thousands of other applications. Animations provide the visual cues to improve the user experience and are very important when the content or screen state is changing. We have already seen a method in the form of TransitionDrawable, for adding animations in our application. Android adds default animations at most of the places, now we would learn how to add our own animations.

There are various ways of doing animations and we will look at some of them here:

### **Animation Drawable:**

This drawable lets us create frame-by-frame animations. You can mention the order of drawables and mention the time between each animation. Here is an example:

```
<animation-listxmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/rectangle" android:duration="50"/>
    <item android:drawable="@drawable/oval" android:duration="50"/>
    <item android:drawable="@drawable/rectangle" android:duration="50"/>
    <item android:drawable="@drawable/oval" android:duration="50"/>
</animation-list>
```

Now you can use the following code to start the animation.

```
ImageView iv = (ImageView)findViewById(R.id.imageView1);
AnimationDrawable drawable = (AnimationDrawable)iv.getDrawable();
drawable.start();
```

In case we want our animation to run only once, we should set the oneShot property of animation drawable to true.

### **View Animations:**

View animations are present in android.view.animation. Android have had these since the beginning. Using these animations you can do actions like move, rotate, scale etc. on views. These should be enough to help us achieve a lot of our tasks. Let’s look at some examples:

#### **Alpha:**

We can use this to change the alpha value of a view. Here is an example:

```
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
    android:toAlpha="1"
    android:fromAlpha="0"
    android:duration="2000"
    android:startOffset="2000">
</alpha>
```

Any view, which animates using this, will go from invisible \(alpha 0\) to completely opaque \(alpha 1\). Animation will start 2 seconds after we call animate on it and animation will run for 2 second.

Here is the java code run this on a textview:

```
TextView tv =(TextView) findViewById(R.id.textView1);
Animation a = AnimationUtils.loadAnimation(this, R.anim.alpha);
tv.startAnimation(a);
```

We can use the same java code for any kind of animations.

#### **Rotate**: 

We can rotate any view by using this. Here is some example code:

```
<rotate xmlns:android="http://schemas.android.com/apk/res/android"
    android:startOffset="1000"
    android:duration="1000"
    android:fromDegrees="0"
    android:toDegrees="360"
    android:pivotX="40"
    android:pivotY="40">
</rotate>
```

Here pivotX and pivotY are the X, Y coordinates of point around which the view is rotated.

#### **Scale**: 

Here is an example:

```
<scalexmlns:android="http://schemas.android.com/apk/res/android"
    android:fromXScale="1"
    android:toXScale="2"
    android:fromYScale="1"
    android:toYScale="2"
    android:duration="1000"
    android:startOffset="1000">
</scale>
```

#### **Translate**: 

Here is an example:

```
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromXDelta="30"
    android:toXDelta="200"
    android:duration="1000">
</translate>
```

#### ** Set:**

Animator set is a combination of all above. You can add more than one of these animations and they will be executed based on startOffset and durations. 



#### **Interpolators:**

In all the above examples, animations take place at constant speed from the start to the end. You can change this behaviour by setting the interpolator property in the XML. By default, LinearInterpolator is used, but you can choose from various interpolators like AccelerateInterpolator, OverShootInterpolator etc.



**Limitations**: Let’s look at some limitations of view animations.



**1.**By now you must have realized that the views end up going to their original places and configs. In order for the final configuration of animation to persists, you can use setFillAfter\(\) function.



**2.**Change your textview to a button and try clicking on the translated view. The issue here is that view animation only change the display, they  don’t really move the button. You will have to change the actual  location once the animation finishes. 



**3.**View animations  can only apply to views. You can’t animate content within the view.



**4.**You have these 4-5 fixed properties that can be animated. These are the most commonly used properties, so they would suffice most of times. 



**d.Shared View Animations:**These are used when there are common elements between two activities and this  animation ensures a smooth transition and a feel of continuity.



**Note that the shared element transitions require Android 5.0 \(API level 21\) and above and will be ignored for any lower API versions.**



The steps to follow are:



** i.Enable Window Content Transitions in your styles.xml file:**



&lt;!-- Base application theme. --&gt;

&lt;stylename="AppTheme"parent="Theme.AppCompat.Light.DarkActionBar"&gt;  
&lt;!-- Customize your theme here. --&gt;  
&lt;itemname="android:windowContentTransitions"&gt;true&lt;/item&gt;  
 ...

&lt;/style&gt;



** ii.**Assign a common transition name to the shared elements in both layouts. Use the android:transitionName attribute. For example, in main\_activity.xml:



&lt;android.support.v7.widget.CardView...&gt;  
&lt;ImageView  
android:id="@+id/ivProfile"  
android:transitionName="profile"  
android:scaleType="centerCrop"  
android:layout\_width="match\_parent"  
android:layout\_height="160dp"/&gt;  
&lt;/android.support.v7.widget.CardView&gt;



and in detail activity:



&lt;LinearLayout...&gt;  
&lt;ImageView  
android:id="@+id/ivProfile"  
android:transitionName="profile"  
android:scaleType="centerCrop"  
android:layout\_width="match\_parent"  
android:layout\_height="380dp"/&gt;

&lt;/LinearLayout&gt;



** iii.Start Activity:**While calling the intent for the detail activity, put the image as an option and bundle it with the intent:



Intentintent=newIntent\(this,DetailsActivity.class\);  
// Pass data object in bundle & populate details activity  
ActivityOptionsCompatoptions=ActivityOptionsCompat.  
 makeSceneTransitionAnimation\(this,\(View\)ivProfile,"profile"\);  
 startActivity\(intent,options.toBundle\(\)\);



** iv.To get the same\(reverse\) effect on back press:**In detail activity, override the back pressed function \(in menu and hardware\).



@Override  
publicbooleanonOptionsItemSelected\(MenuItemitem\){  
switch\(item.getItemId\(\)\){  
// Respond to the action bar's Up/Home button  
caseandroid.R.id.home:  
 supportFinishAfterTransition\(\);  
returntrue;  
}  
returnsuper.onOptionsItemSelected\(item\);  
 }



For multiple shared components, we need to create pairs and pass them as such.



**e.Property Animations:**Google introduced new animation library, in order to  fix all the issues that we saw with view animations. This was introduced with API 11 i.e. Android 3.0. Some of the features were added in API 12 as well. Support library doesn’t provide backward compatibility for these. You might want to have a look at[http://nineoldandroids.com/](http://nineoldandroids.com/). This library provides these new features to old API’s till API 1. Let’s discuss various components of the new system. Here for demonstration, we will try to emulate a bouncing ball.



** i.Value Animator:**This is the main timing engine in the new framework. This has control over the whole animation and it takes care of the time interpolator as well. Let’s see some code to animate a bouncing ball.



animator= ValueAnimator.ofFloat\(intialY,finalY\);



This gives us a value animator with values between the initial Y position and the final Y position.



** ii.Time Interpolator:**Time interpolator gets called from the Value Animator with a fraction value from 0-1 based on the elapsed time. Time interpolator now will now adjust this value. For example, if we have asked for AccelerateDecelerateInterpolator, it will reduce the past fraction to a lower number when called towards the start or the end of the animation. This is because in this interpolator most of the work is done during the middle duration.



In our case we would like the ball to decelerate while going up, so let’s use the DecelerateInterpolator:



animator.setInterpolator\(**new** android.view.animation.DecelerateInterpolator\(\)\);



** iii.Type Evaluator:**The type evaluator tells us the value of the property getting animated as a function of the value of the adjusted time elapsed. Some examples of evaluators are IntEvaluator, ArgbEvaluator etc. We can define our own evaluators as well. In our case the default FloatEvaluator will suffice. 



** iv.Animator Update Listener:**This listener is notified for every frame change and is expected to update the values passed by the Animator. Let’s implement the update listener in the main activity itself. 



animator.addUpdateListener\(**this**\);

**publicvoid**onAnimationUpdate\(ValueAnimator arg0\) {

iv.setY\(\(Float\)arg0.getAnimatedValue\(\)\);

}



Here we are just setting the Y value of the imageView.



Now to make the complete bounce flow, lets implement another interface i.e., the AnimatorListener interface. Inside the animation end function, we would like to change direction.



**publicvoid**onAnimationEnd\(Animator anim\) {

ValueAnimator v = \(ValueAnimator\) anim;

**float**x = \(Float\) v.getAnimatedValue\(\);

**if**\(x ==finalY\) {

 godown\(\);

 }**else**{

 goup\(\);

 }

}



godownand goup functions have the corresponding animation code.



** v.Object Animator:**This is a subclass of value animator and we will be using this very frequently. This class lets us set the object and the property, which we want to animate. For example, we can make our button move from X= 200 to X= -200 to finally settle at the center. 



ObjectAnimator move =

ObjectAnimator.ofFloat\(button,"translationX", 200f, -200f, 0f\);



** vi.Animator Set:**This class lets us combine different animations and play them sequentially, together or with some delay between them. E.g. here we are combining few animations together.



ObjectAnimator out =

ObjectAnimator.ofFloat\(iv,"alpha", 0f\);

ObjectAnimator move =ObjectAnimator.ofFloat\(tv,

"translationX", 200f, -200f, 0f\);

ObjectAnimator in = ObjectAnimator.ofFloat\(iv,

"alpha", 0f, 1f\);

AnimatorSet animationSet =**new**AnimatorSet\(\);

animationSet.play\(move\).before\(in\).after\(out\);

 animationSet.start\(\);



** vii.View Property Animator:**This is an easy concise way to run multiple animations simultaneously. Here is single line code to do the upward ball bouncing code.



iv.animate\(\).translationYBy\(200\).

setDuration\(500\).setInterpolator\(**new**

AccelerateInterpolator\(\)\).withEndAction\(godown\);



Here the last argument is the runnable that will be run when this animation gets over.



** viii.Activity transition:**After calling startActivity, we can override the transition between the screens. For this we will have to add two animations, one for the view going out and the other for the view coming in. After which we can just use this code: 



overridePendingTransition\(R.anim.slide\_in, R.anim.slide\_out\);



This will make the transition according to these animations instead of the default android animations. We will have to do something on our way back from the detailed activity. For that we need to override the onBackPressed function and add similar line their. Similarly for fragment transactions you can set the custom animations on the FragmentTransaction.

