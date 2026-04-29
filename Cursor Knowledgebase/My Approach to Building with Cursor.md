I've tried out a lot of different work flows for building with AI but I think the best way to actually do it is how I've been developing with ROS2 and AI

First, we need to set up our project directory and understand how to add files and edit these files and what changes these files will reflect on on the app itself. 

Once we set this up we should be able to one shot most of our screens with the help of Claude and chat gpt but making sure to single out features when implementing rather than whole screens

One thing I want to learn how to implement are context files in cursor and how to use them effectively. 
- I don't actually think this is too important unless I only use cursor for development which I don't plan on doing
- Instead I want to be able to develop features by using LLMs like Claude and chat gpt to actually develop the app 

### File Routing with Expo
Routing in react native is similar to routing in next js

A file within the app folder represents the route and the file name is the URL that points to that route. This is what allows us to navigate from one page to another page within in app based on a click or slide motion

The app.index.tsx is the home page of the app and represents the starting point of the app

### React Native Components
A component in react native is a reusable building block of the user interface. We can think of it as a function or a class that returns some JSX which is a mix of JavaScript and XML-like syntax that describes how the UI should look

In simple terms:
- Components take an input called props
- They return JSX which is what should appear on the screen
- They can have a state (data that can change over time)
- They can be reused and composed to build complex UIs