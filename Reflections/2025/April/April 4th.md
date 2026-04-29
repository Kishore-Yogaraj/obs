Satisfaction, Discipline and Anxiety.

Yesterday was  terrible day. I don't know what it is but sometimes I just can't pull myself together to get work done. I was so lethargic and I had no idea why. My anxiety was spiking all day I had to go home and take a nap to get it out of my system. 

I woke up and still felt such a rush of anxiety. I couldn't even sleep through the night properly because of it. 

Let's start from the beginning.

I had my call with Eddy and found out he already referred me to Polymath which is amazing. But after this call, I felt satisfied right away and I have no idea why. I acted like I had already secured the job even though I didn't. This put me into a state of satisfaction and doom scrolling and other dopamine spikes that I shouldn't have experienced in the first place. 

This was the start of horror. 

I didn't go to work the next day and was basically useless all day. The day after I skipped my workout and went into work at 1 and felt lethargic the entire day. It's so weird because I started the week off really strong and motivated and I got burnt out after the call with Eddy. It got me thinking. 

What do I really want to do with my life?

I was thinking about if I got the job in California. For as long as I can remember it's been my dream to work in California but now I'm not so sure. After I get California, what's next? I keep saying I want to start my own business but I don't even know how to do it. I keep saying I want to work in robotics but do I really want to work in robotics? When I start to ask myself these questions I get really overwhelmed because I see people doing great things building apps and doing random businesses. I think my circle is too small. All my ideas are all engineering related but there's a lot more ways to get rich. Not everyone is an engineer. But people get rich doing all sorts of things.

I realized that California may not even be the ideal destination for me. While I was wasting my life away yesterday, I watched videos about life in California and realized that it may not be everything I expected it to be. Apparently it gets really cold there. It's super windy. It rains like crazy. I think the life I want to live is in Southern California. 

I always thought I had a clear picture of what I wanted my life to be. I want the big penthouse in the city, I want the warm weather, I want the nice cars, but how do you get there. Is there a proper way to get there? What is my journey supposed to be? I'm lost....

I feel like doing leetcode is such a waste of my life. I would do it simply to get a job but I don't want to work for someone else the rest of my life. Every time I go on twitter I feel like I'm behind. I see all these people making bank just building stupid apps and I'm here grinding away for a job I don't even know that I'll like.

I feel like everyday I question my worth and what destiny has in store for me. I don't know what I want to do with my life and it's killing me. I'm so so scared of wasting away my 20s. I feel like I have no time and I'm already behind. 

Writing this reflection really hasn't helped me with anything because I still feel lost. At the end of the day I'm supposed to be chasing riches, so why do you want to go to California so bad. My life feels so weird right now. Nothing feels right. I don't even know if what I'm working on is something interesting to me. I don't know if all the work I'm doing is to just get a job in California.

I read this blog by levels in which he talked about travelling a lot when you're young and starting some online business to bring you out of capitalism. I have done neither. I feel like I have the opportunity to and I just don't capitalize on it. 

I honestly just don't know what the purpose of my life is. I feel like I'm falling behind and I'm so lost. I go back to Canada in 3 weeks and I haven't achieved any of my goals yet. You know what's really crazy though. A lot of these kids at waterloo are building businesses while grinding leetcode. So it's definitely possible to do it. These kids are working at places like shopify and twitter while also building their own start ups. I guess the difference is that I want a couple things right now.

Finish the savor app
Finish nerf navigation
Finish LSTM project
Build an animation app
Do leetcode

Yeah holy shit that's a lot to do in a month. I'm already so overwhelmed by all of this too. Once school starts, I have even more to work on and they're mainly on things I don't even care about. 

Reading this back I'll see how all over the place I am and it's actually terrible. I'm lost, I'm lost, I'm lost. 

So many ideas, 0 execution, 0 motivation, 0 focus. What is my life bro.

Twitter really does kill my confidence a lot of the time. I see all these people doing crazy things and I'm just stuck in the same loop. 

Okay, I think this a late night thought for you to have you need to get some work done because some progress is better than no progress.

Here's the plan for today:
So we trained a new nerf model with 200 epochs and now we have one goal. How do we localize this thing. The code for localizing is all available to you you just have to find out how to use it.

We learned that the model performs well in places that are 1 meter by 1 meter by 1 meter so now it's time to figure out how to localize this. When training this model, we gave it a scale and we gave it a bound. These two values are important for us to be able to localize properly.

The goal here is to input a world coordinate and see if get that same coordinate rendered in the nerf. Here's the plan for today.

I'm not sure if the nerf model actually trained properly because I can't see the object mesh so what we're going to do is run the occupancy grid node on the nerf model and see if it's able to identify the objects. If it's able to do that then we know we have a pretty decent nerf model. Let's play around with the density thresholds and the z thresholds to figure this out. 

After we do this we need to see if we can even render the nerf using the world coordinate. If we get a good result this would be amazing progress. 

After this we attempt to localize using a point slightly off from the camera coordinate and see if we're able to find the actual coordinate. Let's grind this out and then worry about life after. Here we go. 

### Issues
I think the issue might be that we used a gazebo simulation environment and the environment is too perfect once again. It looks like we need some variation, similar to how there's variation in the blender environment