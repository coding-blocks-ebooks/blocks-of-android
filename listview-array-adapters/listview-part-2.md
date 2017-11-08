#Custom ListView & Array Adapters

##Understanding Custom Array Adapter 

If you have an array of general objects and want to display multiple items from each object or items which are Stings or not Strings then you need to create a custom ArrayAdapter.


* Build a new Android Project 
* Open up ``` app - res - drawable``` & add any ```Image```into it . 

##Creating a  Custom Layout

* Create a new Layout resource file.

 Open up ``` app - res - layout - new - Layout Resource File - custom_row```

* Add an ImageView and a Text view and give them their respective Id's . 

```custom_row.xml code here```



##Creating a Custom Array Adapter 

* Create a new Java class.

Open up `` app - java - new Java file``

Now, the first thing that we need to do is that we need to extend this class to an ArrayAdapter .

```java
class CustomAdapter extends ArrayAdapter<String> {
}```

* Create a constructor.

* Inside your CustomAdapter class press ```Alt+Insert``` and go to ```constructor``` 

* You'll get a constructor like this. 

```java 
public CustomAdapter (Context context , int resource){
super(context , resource);
}```

* Now , we'll be making changes inside the constuctor itself.

* Insted of the ```int resource``` we want to pass on the array which has our items.

```java 
public CustomAdapter (Context context , String[] fruits ){
super(context ,R.layout.custom_row, fruits);
}```

* Now we're going add override methods .

* Go , outside of your constructor and ```Alt+Insert``` for ```override methods```

* Now , the method which we're overriding is ```getView()``` 

* Now, we're going to create a custom list adapter and going to associate with our array with that adapter , so all the items can be displayed in device screen . 

* Now , we need to initialize our view .

```java 
LayoutInflater lf = LayoutInflater.from.(getcontext());```

* Now , we need to initialize our customview .

```java 
View CustomView = lf.inflate(R.layout.custom_row ,  parent , false);```

* Now , the parameters it is going to take as input , 

* R.layout.custom_row : our custom layout 

* parent : default parameter

* false : default parameter 

Now we're going to create a ```String``` which is going to get the item depending upon it's position .

```java
String singleItem = getItem(position);
```


* Now the next thing which we've to do is to get the reference of the ```ImageView``` as well as the ```TextView``` 

   ```java
   TextView tv = (TextView)  CustomView.findViewById(R.id.text);
   ImageView iv = (ImageView) CustomView.findViewById(R.id.image);
   ```
   
* Now we've to set this text to this ```singleitem``` because the string which actually the item by using position.
 
 ```java
 tv.setText(item);
 iv.setImageResource(R.id.drawable.image)```
 
  






 
  





