d### Intro
In this project, we’ll walk you through building a fully functional Meetup clone app using React Native, Expo, Expo Router and Supabase. Whether you're interested in learning how to create a local event app or just looking to improve your full-stack development skills, this tutorial will equip you with the tools you need. We’ll cover everything from setting up authentication to managing real-time data and creating a simple user interface.
### Tech Stack
- React native
- Expo
- Expo Router
- Nativewind (styling library - allows us to use tailwind css styling with react native)
- Supabase
	- Built on top of postgres
### Main screens
- List screen
- Details page
- Joining event
### Tutorial Series
#### Part 1: Building Authentication, Navigation, and Basic UI
In the first part of the series, you’ll learn how to set up the project, work with React Native fundamentals, Expo Router for smooth navigation, and integrate Supabase for user authentication and database management.
#### Part 2: Advanced Features - Maps, GEO Filtering, Event Creation and Backend Enhancements
The second part dives deeper into advanced features like integrating maps, implementing GEO filtering to fetch nearby events, and using Supabase to handle more complex backend logic such as filtering, sorting, pagination, and event creation.

### What I'm going to learn
#### **React Native Fundamentals:**[](https://www.notjust.dev/projects/meetup#react-native-fundamentals)
Start with the basics of setting up your environment and getting hands-on with React Native, one of the most popular frameworks for building cross-platform mobile apps.
#### **Seamless Navigation with Expo Router:**[](https://www.notjust.dev/projects/meetup#seamless-navigation-with-expo-router)
Learn how to use Expo Router to create a smooth and intuitive navigation experience that mimics the simplicity and functionality of popular apps.
#### **Backend Integration with Supabase:**[](https://www.notjust.dev/projects/meetup#backend-integration-with-supabase)
Learn how to handle backend services, including user authentication, database management, and real-time updates using Supabase. By the end, you'll know how to connect and interact with your backend seamlessly.
#### **Maps and GEO Filtering:**[](https://www.notjust.dev/projects/meetup#maps-and-geo-filtering)
In the second part of the series, you’ll integrate maps into your app and learn how to fetch and display nearby events using GEO filtering.
#### **Advanced Backend: Filtering, Sorting, Pagination:**[](https://www.notjust.dev/projects/meetup#advanced-backend-filtering-sorting-pagination)
You’ll also implement advanced backend features such as filtering, sorting, and pagination to handle larger datasets and make the app more efficient.
#### **UI Design and Development:**[](https://www.notjust.dev/projects/meetup#ui-design-and-development)
Explore modern UI design patterns and how to implement them in React Native to create a clean and user-friendly interface that improves the overall app experience.
#### **State Management with React Hooks and Context API:**[](https://www.notjust.dev/projects/meetup#state-management-with-react-hooks-and-context-api)
Learn to manage your app's state effectively, ensuring your app runs smoothly even as it scales in complexity.

### App Directory
- All screens shown in the app are defined in the **app directory**
- This directory is specific to expo router (which has file based navigation)
- This means that all the files defined in the app directory will create their respective screens on the app
- Files are created in the app directory and the expo router then sets up the screens in the actual app
- Most of our time will be spent in the app directory
	- Specifically the `index.tsx` tab which will be responsible for the first tab
- **The app directory is specifically used for screens, layouts, and anything to do with navigation. Not individual components on the screen **
### Dummy Data
- At first we're going to focus on the UI
- This means that the data we use for now will be **dummy data** instead of fetching data from a backend
- We have a prepared list of events to work with which we will later replace this static list with data fetched from **Supabse**

### Working on the UI
- The first part is to start by building the list of events but we first have to find a way to render 1 event 
- It's good to think of every component as a small piece of a larger feature
- In this case let's try to render 1 event before we try to render multiple events
![[Pasted image 20250225155313.png]]
- It's a good idea to put all the content on the screen first and then find a way to style them and group them
- We can use: https://icons.expo.fyi/Index for different icons that we might want to include in our app
- You can also use this for different icons: https://lucide.dev/

### Styling
- After getting all our contents on the screen we can start working on adding some styling like bolding the text
- Tailwind is a simpler way to add styling to your app instead of using built in javascript styling features
	- Tailwind allows you to use different classes to style your features such as text and images 
	- Tailwind also doesn't use numbers to define pixels but uses "levels" so that features stay more aligned when designing
	- Here's an example `<Text className="text-5xl font-bold ml-5">This is the title</Text>`
### Grouping
- How do we group elements together in order to get our desired layout?
- Groupings are done with the use of styling classes as well such as padding, gap, flex, etc.
- It's good to imagine different pieces of text or contents on a screen as different **containers**
- Each of these containers are created using the `<View>` element which applies styling to any components within this container
### Components
- Now that we created one feature of the app, we can recognize that this is a feature that will have to be created over and over again which makes this a component now
- Instead of copying and pasting the same piece of code over and over again, we can use **components** to ensure the reusability of various pieces of code 

### Using the Data from Our events.json file
- To do this we import the the json file into our component file and now we're able to parse through the JSON as if it was an array
### Using DayJS to render the date time
- The DayJS library allows us to format the time and render it in a way that is human readable

### Event Details Page
- Every time a user clicks on an event we want to redirect them to their own details page which is similar to how we will have a deals detail page as well as a restaurant details page
- To make this work we can use the **onpress** handler which will redirect us to another page
- We also need a way to send data to the clickable containers so that it knows what event we specific clicked on

# Backend
**Password:** 5jZoJndzgieh6KY5

Let’s make our app functional and integrate the backend part:
- Auth
- Database
- API
For all of that, we will use [Supabase](https://supabase.com/) - an open-source alternative to Firabase. What I like about Supabase is that it is built on top of Postgres which makes it super powerful.

### Building Authentication
- This will allow users to create an account, add details and manage the events they want to go to
- We need to then store this information in a database (this data base will relate the information from user to each event)
- We then need an API that will actually filter and fetch the data we specifically want

### Setting up Database Schema
- Postgres is a relational database and operates with a very strict schema compared to other NoSQL data based
- With supabase we need to identify what exactly what kind of data the tables are going to hold, how the data will be structured, how the data connects with one another, etc.

### Editing Profile Page
In these steps we will attempt to make it so that users can edit the profile page. We will need them to **show profile details, update profile info, and add an avatar**

### Retrieving Events from the backend
This is a pretty important step and we should definitely learn it out completely. This step will help us understand how to retrieve data from a backend which will effectively be the restaurants we add as well as the deals they have to offer. In this section, we will learn how to create our own data and fetch our own data which is what we need to be able to do for the restaurants

### Schema
The first step in creating a backend to fetch and receive data is to develop a backend schema. But what is a schema?

A schema is a structural blueprint of a data base that defines how data is organized, stored and related. You can think of this as the architectural plan the shows how all pieces of your data fit together. The backend schema typically refers to this database organization plus the logic for how the application interacts with that data.

A data base schema consists of:
- Tables
	- These are like spreadsheets that hold specific types of data. For example, a "Users" table or a "Products" table
- Fields (columns)
	- Each table has multiple fields that define what attributes that entity has. A User might have fields for name, email, password and creation date
- Data Types
	- Each field has specific type of data it can store: integers, strings, dates, booleans, etc. This helps maintain data integrity
- Relationships
	- The connections between different tables. For example, how a "User" relates to "Orders" they've placed

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (1, CURRENT_TIMESTAMP, 'React Native Workshop', 'Learn the basics of React Native and build your first app.', '2024-10-10T14:00:00', 'Tech Hub, Barcelona', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/1.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (2, CURRENT_TIMESTAMP, 'Supabase for Beginners', 'A beginner''s guide to Supabase and how to integrate it with your apps.', '2024-10-11T16:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/2.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (3, CURRENT_TIMESTAMP, 'Building APIs with Supabase', 'Learn how to create powerful APIs with Supabase.', '2024-10-12T18:00:00', '123 Tech Street, New York', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/3.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (4, CURRENT_TIMESTAMP, 'Advanced React Native UI', 'Dive deep into advanced UI components and animations in React Native.', '2024-10-13T10:00:00', 'Innovation Center, Berlin', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/4.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (5, CURRENT_TIMESTAMP, 'AI Integration in Apps', 'Discover how to integrate AI into your mobile applications.', '2024-10-14T15:00:00', 'AI Labs, London', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/5.jpeg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (6, CURRENT_TIMESTAMP, 'Intro to Expo', 'Get started with Expo and learn how to build cross-platform apps.', '2024-10-15T12:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/6.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (7, CURRENT_TIMESTAMP, 'JavaScript Essentials', 'Master the fundamentals of JavaScript for web and mobile development.', '2024-10-16T09:00:00', 'Coders Arena, Madrid', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/7.jpeg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (8, CURRENT_TIMESTAMP, 'Mastering Mobile App Design', 'A complete guide to designing beautiful and user-friendly mobile apps.', '2024-10-17T17:00:00', 'Design Studio, Paris', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/8.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (9, CURRENT_TIMESTAMP, 'Firebase vs Supabase', 'An in-depth comparison of Firebase and Supabase for backend development.', '2024-10-18T14:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/9.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (10, CURRENT_TIMESTAMP, 'Full-Stack React Native Development', 'Build full-stack mobile apps using React Native, Supabase, and Node.js.', '2024-10-19T11:00:00', 'Tech School, Los Angeles', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/1.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (11, CURRENT_TIMESTAMP, 'Optimizing App Performance', 'Learn how to improve the performance of your React Native apps.', '2024-10-20T13:00:00', 'Dev Conference, San Francisco', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/2.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (12, CURRENT_TIMESTAMP, 'React Navigation Deep Dive', 'Understand the inner workings of React Navigation and how to implement complex navigations.', '2024-10-21T16:00:00', 'Tech Lab, Amsterdam', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/3.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (13, CURRENT_TIMESTAMP, 'TypeScript for React Native', 'Enhance your React Native code with TypeScript for better development.', '2024-10-22T09:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/4.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (14, CURRENT_TIMESTAMP, 'GraphQL with React Native', 'A hands-on session to learn how to integrate GraphQL into React Native apps.', '2024-10-23T12:00:00', 'Tech World, Tokyo', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/5.jpeg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (15, CURRENT_TIMESTAMP, 'State Management in React Native', 'A comprehensive guide to managing state with Redux and Context API.', '2024-10-24T10:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/6.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (16, CURRENT_TIMESTAMP, 'Testing React Native Apps', 'Best practices for testing and debugging React Native applications.', '2024-10-25T14:00:00', 'Developer Hub, Sydney', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/7.jpeg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (17, CURRENT_TIMESTAMP, 'Deploying React Native Apps', 'Learn how to deploy your React Native apps to App Store and Google Play.', '2024-10-26T11:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/8.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (18, CURRENT_TIMESTAMP, 'Managing App Releases', 'A step-by-step guide to versioning and managing app releases.', '2024-10-27T13:00:00', 'Tech Conference, Toronto', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/9.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (19, CURRENT_TIMESTAMP, 'Custom Components in React Native', 'Learn how to build reusable custom components for your React Native projects.', '2024-10-28T15:00:00', 'Dev Meetup, Dublin', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/1.jpg');

  

INSERT INTO "Events" (id, created_at, title, description, date, location, image_uri)

VALUES (20, CURRENT_TIMESTAMP, 'Building Cross-Platform Apps', 'A guide to building apps that run on both iOS and Android using React Native.', '2024-10-29T16:00:00', 'Online', 'https://notjustdev-dummy.s3.us-east-2.amazonaws.com/images/2.jpg');

  

ALTER SEQUENCE "Events_id_seq" RESTART WITH 21;