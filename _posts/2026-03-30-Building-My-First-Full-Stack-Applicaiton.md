---
layout: post
title: Building My First Full Stack Application, A Habit Tracker
subtitle: What I Learned Building My First Full Stack App From Scratch
---

I built my first full stack application. It was a CRUD-based habit tracker, and while that might sound simple on the surface, it taught me more about how software systems actually work than anything else I've done so far. This post is a breakdown of how I built it, what I struggled with, and what I learned along the way.
 
---
 
## Starting With the Database
 
Before writing a single line of code I had to figure out what data I actually needed from the user and how that data would relate to each other. That planning stage is where the database comes in.
 
I think of the database as the nucleus of any application. Everything revolves around it. It's where user data gets stored, retrieved, and persisted. Without it you just have a pretty interface with nothing behind it.
 
For this project I used PostgreSQL configured through Prisma ORM. Prisma acts as the middle ground between my application and the database. Instead of writing raw SQL queries I can write JavaScript and Prisma translates that into database operations and returns the results back to my application.
 
I created three tables: User, Habit, and CheckIn. The User table handles authentication and authorization. The Habit table stores each habit's name, current streak, and longest streak. The CheckIn table is where it gets interesting because it shares a foreign key relationship with the Habit table. A foreign key is what keeps two tables synchronized with each other. So every CheckIn record knows exactly which Habit it belongs to. That relationship is what makes the toggling feature work.
 
---
 
## Business Logic With Express.js
 
Once the database was defined the next step was figuring out how the application would interact with it. That's where Express.js came in.
 
Express is a Node.js framework that lets you define routes that listen for incoming requests. When a request comes in the server makes a query to the database through Prisma and sends a response back to the client with the requested data.
 
For the Habit routes I implemented full CRUD:
 
- **POST** to create a new habit and store it in the database
- **GET** to retrieve all habits for the current user
- **PUT** to update the streak and longest streak values
- **DELETE** to remove a habit entirely
 
For the CheckIn routes I kept it focused on two operations. A **POST** request creates a new check-in record tied to a specific habit and today's date. A **DELETE** request removes it. Together these two operations create the toggling effect you see in the UI where clicking a button marks a habit complete or undoes it.
 
I also separated my routes into individual files, `authRouter.js`, `habitRouter.js`, and `checkinRouter.js`, rather than putting everything in one file. This made the codebase much easier to navigate and maintain.
 
---
 
## Building the Client Side
 
The client side is where the user actually interacts with the application. This was the most challenging part for me personally since my background is stronger on the backend side.
 
The first thing I focused on was creating data. Before you can display anything you need something to display. I built a dialog form that collects the habit name from the user. On submission it fires a POST request to the server which stores the new habit in the database. After that the page re-renders with the updated list using a skeleton loading effect while the data is being fetched.
 
For the habit display I went with a card-based layout. Each card shows the habit name, current streak, longest streak, and two buttons. One button toggles between "Done Today" and "Checked In" depending on whether the user has completed that habit today. The other removes the habit entirely.
 
The toggling button was the most complex piece of the UI. When the state is "Done Today" it fires a POST request to create a check-in for today's date, recalculates the streak by walking backwards through all previous check-ins counting consecutive days, and fires a PUT request to update the streak values. The page then re-renders with the updated information and the button switches to "Checked In."
 
When the state is "Checked In" it fires a DELETE request to remove today's check-in, recalculates the streak starting from yesterday, and fires another PUT request to update the values back. The longest streak never decreases. It's a permanent record of the best consecutive run ever achieved.
 
One thing I learned early on is to always use the response data's ID as a data attribute on your UI elements. That ID is what tells you which piece of data you're actually interacting with when a user clicks a button. Without it you have no way of knowing which habit or check-in to target.
 
I also made sure every async operation disables the relevant buttons while the request is in flight to prevent duplicate submissions. A skeleton loading screen appears on every page load so users always have visual feedback that something is happening.
 
---
 
## Authentication and Authorization
 
Authentication and authorization are topics that deserve their own deep dive because they go far beyond just checking a username and password. There's session management, token verification, password hashing, and a lot more complexity underneath the surface.
 
Rather than building all of that from scratch I used Auth0, which is an Identity and Access Management provider. Auth0 handles the heavy lifting, token creation, session management, and password hashing, so I could focus on integrating it into my application rather than reinventing the wheel.
 
The setup process involved creating an Auth0 account and configuring my application's credentials into the Express server. From there Auth0 provided three routes automatically: `/login`, `/logout`, and `/callback`. The callback route is where things get interesting. After a user attempts to log in Auth0 verifies their identity and sends an authorization code back to the `/callback` route. My server then exchanges that code for a session token and redirects the user to the application.
 
It's important to understand the distinction between authentication and authorization. Authentication answers the question "who are you?" It verifies that the person logging in is who they claim to be. Authorization answers the question "what are you allowed to access?" It controls which resources a verified user can interact with.
 
In practice this means every API route in my application checks that the authenticated user's ID matches the data they're requesting. User A cannot see or modify User B's habits even if they know the habit ID. This is what's called a multi-tenant architecture, where many users share the same application while each having completely private access to their own data.
 
---
 
## Deployment and the UTC Timezone Bug
 
For deployment I used Render to host the application. During this process I switched from SQLite to PostgreSQL since PostgreSQL is the standard for production environments. I connected my GitHub repository to Render and configured the build process to run migrations automatically on every deployment.
 
Everything worked perfectly on my local machine. Then I deployed it and the check-in feature broke completely.
 
The bug came down to a timezone mismatch. On my local machine I'm in Pacific Time which is UTC-8. When I clicked "Done Today" at 7:00 PM Pacific the date was March 24th locally. But when that timestamp got converted to UTC it became March 25th at 3:00 AM. The PostgreSQL database on Render stores everything in UTC so the date being saved didn't match what my client-side streak algorithm was looking for.
 
The fix was to standardize everything on UTC across the entire stack. The server now normalizes all incoming dates to UTC noon before storing them. The client-side streak calculation compares dates using UTC methods instead of local time methods. Once everything spoke the same timezone language it worked correctly.
 
That bug taught me something valuable. Always think about where your code is running and what environment assumptions it's making. Code that works locally isn't always code that works in production.
 
---
 
## What I Learned
 
This project taught me more than I expected. On the technical side I learned how to design a relational database schema, build a RESTful API, implement authentication and authorization, write a custom algorithm with real edge cases, handle timezone differences across environments, and deploy a full stack application to production.
 
But the bigger lesson was about problem solving. When the UTC bug hit I didn't panic. I went into debugging mode, read the error messages, traced the data flow, and found the root cause. That process of breaking a problem down and isolating the issue is something I'll carry into every project going forward.
 
The client side pushed me outside my comfort zone and honestly I enjoyed it more than I expected. DOM manipulation, event handlers, async fetch requests, these things clicked for me in a way they hadn't before because I was building something real with them.
 
This project is a CRUD application at its core but the decisions made along the way, the streak algorithm, the timezone handling, the multi-tenant architecture, the Auth0 integration, made it significantly more than that. And that's exactly the kind of project I want to keep building.
 
---
 
**Tech Stack:** HTML, CSS, Vanilla JavaScript, Node.js, Express.js, Prisma ORM, PostgreSQL, Auth0, Render
