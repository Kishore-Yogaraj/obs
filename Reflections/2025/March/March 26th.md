### Workout
Great work starting off the day today!

You were tired as shit going to the gym today but still managed to push through. This is what its all about. 

You're in a great mood and ready to get to work now which is perfect and exactly what you want to be doing everyday. Very happy with this so far

When you go to bed though actually go to bed. You went to bed at 11 and then stayed on your phone for an hour and then ended up sleeping around 12:30 which explains why you were so tired to be honest.

Let's make sure we get a good night's rest from now on.

Squats felt weird today. It was your heavy day but I didn't really feel to strong. Maybe that's the cut coming into play but I'm not really sure. 

Staying fit is a lot of experimenting but at least you know what works now. The "bulk" you were on was definitely not worth it. You probably didn't even hit your protein most days. Consistency plays a huge role in staying fit.

I'm thinking about doing higher reps on my squats instead so I don't injure myself. I was still able to PR today. I'm getting around 2300 - 2400 calories a day give or take. Playing around with my calorie intake with olive oil because of how calorie dense it is. 

I honestly had no idea. I'm not going to worry too much about hitting 200 grams of protein, I think 180-190 is plenty still. I'm still way under my deficit. Sitting roughly at a 600 calorie deficit which actually doesn't sound too bad. 

I would say let's keep going the way we're doing things right now. Use a table spoon of oil for your chicken and a table spoon and a half for your mushrooms and you should be good. 

Realistically, I think I can leave Germany sitting around 185-190 pounds which sounds like a lot to lose but it's only around 10 pounds. You have around 5 weeks left here so you should be able to lose a decent amount. Getting down to 190 is pretty good still. I think you want to aim to be peak cut around the start of July I would say. 

After doing some research you have a long way to go until you look really lean still. I would say around 170 pounds or even 160 maybe. At the rate you're going you may not actually make this weight. It looks like you're going to sit around 190 when you leave Germany and then push to 180 for July. You can take a really steep cut in the last month (June) but this is also midterm season so highly unlikely. Ideally here you would want to lose around 2 pounds a week but at that point you're not even going to hold onto any muscle or very minimal muscle. 

Realistically, lets aim to leave Germany around 190 pounds I think that's still pretty good, realistic and healthy. 

If we go more than 2 weeks with no progress, then we'll start adding some calories back in. For now, I think we're perfect.

### Paper
The paper is going pretty well too. I think we're ahead of schedule which is nice. This is nothing more than a resume booster to be honest so we'll see how it goes. Getting it submitted is an achievement in itself.

### NeRF Robot
Big day coming up Thursday an Friday. Here's out plan for implementation.

First we have to make sure we train the model properly and account for scaling in both colmap and the nerf training. After we do this we need to do some testing for nerf rendering. What this means is that our one goal right now is to input coordinates in the real world, scale that to the nerf world and hopefully the image we see in gazebo is the same image we see in the nerf model. If this works, then we're on the right track.

After this is the localization step. For the localization step we take two inputs, the predicted state and the image from the RGB camera. The goal here is to optimize the predicted state to correct the predicted state (if it is wrong) using the nerf model as the ground truth.

The workflow for this would be something like:
- Create your localization function
- Make this function take two inputs, a random predicted state that is near the the actual state of the camera in gazebo and the camera input from gazebo
- The NeRF model should then render the nerf from the predicted state (which should be working from the paragraph above)
- The localizer should then see there's a mismatch between the NeRF rendered image and the actual image and then some type of optimization should be applied to match the nerf rendered image to the actual camera input and then it should return a corrected state based on the nerf rendered image
	- This means that we also need to be able to return a coordinate from the nerf rendered image
	- Take a look at the code for this and consider the transformations that need to be applied
		- I think we would go coordinate in real world, transform to coordinate in the nerf, render nerf, optimize with coordinates in nerf, transform coordinates back to the real world with the correct coordinates

If we can get this done on Thursday and Friday it will be really good progress. 

After that we have to create the physics based action commands and then we put them both together and done....

Something is bound to go wrong so lets be patient and learn as we go 


### Today's Plan
- Finish K-fold cross-validation section
- Make the required edits to the different paragraphs
- Work on Savor for the rest of the day
- Order the Pjs for live too 

HOLY SHIT HOLY SHIT HOLY SHIT

TURN THE WHOLE IMAGE GENERATION API INTO AN IOS APP BRO AND PRINT MONEY. ITS ACTUALLY GAME TIME

THIS IS GOING TO BE A FAT RUSH 

I FOUND MY WEEKEND PROJECT

PRINT MONEY, PRINT MONEY, PRINT MONEY,

THIS MIGHT BE THE BIG BREAK


