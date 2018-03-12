# Fragments

Fragments were introduced in Android 3.0 (API level 11) to provide more flexible and dynamic user interface for bigger screens like tablets because tablets screens are much bigger than the mobile screens so the extra space in the tablets can be utilized for some other useful purpose.

 * You can think of Fragment like “Sub-Activity”. Using fragments you can have multiple portions in your screen.

 * Fragment contains its own view just like activity.
 * Fragments can be added to an activity dynamically at the run time.

 * Every Fragment has its own life cycle but since they are embedded in an Activity so there life cycle heavily depends on the life cycle of the Activity they are embedded in. Like when an Activity is paused so is the fragment, when activity is destroyed so is the fragment but when the activity is resumed or running you can decide which fragments to add to activity and which to remove.

 * Fragments lives in a ViewGroup insides the activity’s view hierarchy.