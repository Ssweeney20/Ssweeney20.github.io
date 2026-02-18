## CS 499 Capstone ePortfolio  
### Southern New Hampshire University  


## Overview

My name is Sean Sweeney, and this portfolio represents the culmination of my Bachelor of Science in Computer Science at Southern New Hampshire University. Throughout the program, I developed a strong foundation in software engineering, data structures and algorithms, database systems, and secure application design.

This ePortfolio highlights my growth throughout my time at SNHU, and the work included here reflects not only technical ability, but also my approach to problem solving and system design.

---

## Professional Self-Assessment

Completing my Bachelor of Science in Computer Science at Southern New Hampshire University has allowed me to grow from writing small programs to designing and improving a full stack application. Building this ePortfolio gave me the opportunity to reflect on that growth and organize my work in a way that shows both technical depth and practical application.

The primary artifact in this portfolio, InTheTow, represents that progression. What started as an idea for a application to help truck drivers share warehouse information evolved into a more complex system and full stack application. I built this application over the course of a few months, and added enhancements during my capstone course. Instead of presenting unrelated assignments, I chose to improve a single application across software design, algorithms, and databases. This approach demonstrates my ability to think long term and make architectural and system design decisions.

Throughout the program, collaboration and communication were just as important as coding. In courses using simulated Agile practices, I participated in sprint planning, wrote user stories, and worked in roles such as Scrum Master and Product Owner. These experiences strengthened my ability to communicate requirements clearly. I learned that strong software engineering depends on clarity, not just technical correctness.

My coursework strengthened my understanding of data structures and algorithms beyond theory. I applied normalization, weighted averages, and confidence modeling when building the InTheTow Score, translating algorithmic ideas into programmed logic. In my Data Structures and Algorithms class, I worked with common data structures such as arrays, linked lists, stacks, queues, hash tables and trees, and learned how their implementations affect performance. I analyzed time and space complexity using Big O notation and practiced choosing appropriate data structures for improved performance.

In software engineering and databases, I gained hands on experience building RESTful APIs, structuring backend logic, implementing authentication, and integrating multiple data stores in my own full stack application (InTheTow). By incorporating Redis alongside MongoDB, I had to consider performance, and the efficiency of adding a cache. These decisions required thinking beyond basic CRUD functionality and toward system design. Additionally, I learned about SQL databases and the actual theorey behind databases in SNHU coursework.

Security has been a consistent focus throughout both my coursework and development process. In my Software Security class, I learned how to identify vulnerabilities and apply defensive programming practices. I worked with concepts such as input validation, authentication, access control, and secure system design. These lessons carried directly into my projects, where I implemented role based access control, validated API inputs, and enforced user permissions at the backend. The course reinforced the idea that security and testing should be built into a system from the start.

Beyond the artifacts shown here, my coursework included object oriented design projects, graphics programming, reinforcement learning, and mobile application development. These experiences shaped my goal of becoming a well rounded software engineer who understands both system architecture and user facing design.

The three enhancements in this portfolio work together to demonstrate the range of my skills. The first highlights software enginnering and design. The second demonstrates algorithmic design and implementation. The third shows database integration and implementing a cache. Together, they represent my cumulative experience as engineering improvements within a real application.

---

## Code Review

Before implementing enhancements, I conducted a code review to evaluate the existing system, and outline planned improvements. This video can be found below.

Code Review Video:  
[Code Review](https://www.youtube.com/watch?v=jzMBbNSII_c)

---
# Artifact

The central artifact in this portfolio is InTheTow, a full stack application I designed to help truck drivers share information about warehouses and shipping facilities. Users can leave reviews, report dock times, and provide feedback on their appointments to help other drivers make informed decisions.

I selected this application because it represents a real system rather than a standalone academic assignment. Instead of building separate projects for each area, I enhanced this one application across software design, algorithms, and database systems. The following sections explain each enhancement and how it demonstrates growth in specific areas of computer science.

Repository:  
[InTheTow GitHub Repository](https://github.com/Ssweeney20/InTheTow)

---

# Enhancement One  
## Software Design and Engineering  

My artifact (InTheTow) allows users to leave reviews of shipping / receiving facilities, and I enhanced it by adding the ability for users to ask questions on a review and for the original reviewer to respond.

The question and answer feature shows my ability to extend an existing codebase by designing new data schemas, updating backend routes, and integrating the feature into the React frontend without breaking functionality. This improvement makes reviews more useful by allowing follow up clarification instead of relying only on single comments.

This enhancement aligns with the course outcomes I planned to address in Module One, particularly software design and engineering. It required backend adjustments, API design, and frontend state management, and no changes to my original outcome plan are needed.

While working on this enhancement, I learned how even small feature additions can affect multiple layers of an application. One of the main challenges was handling conditional logic around who can ask and answer questions. Overall, this process helped me think more critically about feature design and building systems.

<img width="775" height="528" alt="Screenshot 2026-02-10 at 5 09 46 PM" src="https://github.com/user-attachments/assets/a521a4cd-3dd7-4506-b934-60fddec2a8c1" />

```
const createQuestion = async (req, res, next) => {
    try {
        const { reviewID,
            questionText, originalReviewAuthor
        } = req.body
        const askedBy = req.user;

        if (askedBy === originalReviewAuthor) {
            return res.status(404).json({ message: "Cannot ask question on your own review" })
        }

        const review = await Review.findById(reviewID)

        // do input validation here
        if (!review) return res.status(404).json({ message: "Review not found" });

        // create question
        const question = await Question.create({
            reviewID, askedBy, questionText, originalReviewAuthor
        })

        // save question to review
        review.questions.addToSet(question._id)
        await review.save()


        res.status(201).json(question)
    }
    catch (err) {
        console.error('createQuestion error: ', err);
        next(err)
    }
}

const answerQuestion = async (req, res, next) => {
    try {
        const { answerText, questionID } = req.body

        const question = await Question.findById(questionID)
        question.answerText = answerText
        await question.save()

        res.status(201).json(question)
    }
    catch (err) {
        console.error('answerQuestion error: ', err);
        next(err)
    }
}
```

---

# Enhancement Two  
## Algorithms and Data Structures  

For this enhancement, I designed and implemented the InTheTow Score, a scoring system that summarizes all facility and review data into a single metric.

The scoring system demonstrates my ability to analyze data, normalize values across different scales, and apply weights to produce a fair result. Instead of relying only on raw ratings, the score incorporates multiple factors such as review volume, recency, and other metrics to better reflect overall facility quality.

This enhancement aligns with the algorithms and data structures outcomes identified in Module One. Implementing the score required me to look at backend logic design, consideration of data, and integration into the existing application without disrupting current functionality. No changes to my original outcome plan were necessary.

Through this enhancement, I learned how subjective concepts like “quality” can be translated into structured logic. One of the main challenges was handling incomplete or low confidence data in a way that still produced results. For my algorithm I ended up using a weighted average with normalized values to get them all on the same scale. I also calculated confidence for each value that factored into the score. Overall, this process strengthened my ability to design algorithms that balance technical correctness with real-world usability.

<img width="1290" height="562" alt="Screenshot 2026-02-10 at 5 11 25 PM" src="https://github.com/user-attachments/assets/07a7cbd8-86b9-42a7-83b0-bd28c82c89ca" />

```
const normalizeValue = (min = null, max = null, value, withGrowth = false, midPoint = null, slope = null) => {
    if (withGrowth && midPoint !== null && slope !== null){
        return 1 / (1 + (Math.pow(Math.E, slope * (value - midPoint))))
    }
    else if (min !== null && max !== null){
        return ((value - min) / (max - min))
    }
}

// prior - what score should something have when I don’t trust the data yet?
const calculateConfidence = (prior, confidence, value) => {
    return prior + confidence * (value - prior)
}

const calculateInTheTowScore = (avgRating, numRatings, recentFiveRatings = [],
                                avgTimeAtDock, appointmentsOnTimePercentage, 
                                overnightParking, safetyScore, numSafetyReports, numTimeReports) => 
    {
    // normalize values so all are on same scale
    avgRating = normalizeValue(1, 5, avgRating)
    avgTimeAtDock = normalizeValue(undefined, undefined, avgTimeAtDock, true, 240, 0.012)
    safetyScore = normalizeValue(1, 5, safetyScore)
    // calculate confidences
    const ratingConfidence = normalizeValue(undefined, undefined, numRatings, true, 3, -1.1)
    const safetyConfidence = normalizeValue(undefined, undefined, numSafetyReports, true, 3, -1.1)
    const dockTimeConfidence = normalizeValue(undefined, undefined, numTimeReports, true, 3, -1.1)
    const totalScoreConfidence = ratingConfidence

    // weigh values with confidences
    avgRating = calculateConfidence(0.5, ratingConfidence, avgRating)
    avgTimeAtDock = calculateConfidence(0.5, dockTimeConfidence, avgTimeAtDock)
    safetyScore = calculateConfidence(0.5, safetyConfidence, safetyScore)

    // get average rating to 5 most recent reviews (this score will be weighed more highly then average rating)
    let sum = 0
    for (let i = 0; i < recentFiveRatings.length; i++){
        sum += recentFiveRatings[i]
    }
    let recentAverageRating = sum / recentFiveRatings.length
    recentAverageRating = normalizeValue(1, 5, recentAverageRating)

    // calculate weighted average
    const totalRatingWeight = 0.15
    const recentRatingWeight = 0.4
    const timeWeight = 0.4
    const safeWeight = 0.05

    let weightedAverage = (avgRating * totalRatingWeight) + (avgTimeAtDock * timeWeight) + (safetyScore * safeWeight) + (recentAverageRating * recentRatingWeight)

    // factor in total confidence
    return calculateConfidence(0.5, totalScoreConfidence, weightedAverage) * 100
}
```

---

# Enhancement Three  
## Databases  

For this enhancement, I implemented an activity scoring system using Redis to store and display active facilities.

I chose this artifact for my ePortfolio because it shows database design beyond basic data storage. By integrating Redis alongside MongoDB, I was able to track facility activity using a sorted set and retrieve the most active facilities. In effect, I’m using Redis as a cache, or as a leaderboard. Activity scores are updated based on different user interactions, and the highest ranked facilities are displayed in a “Recently Active Facilities” section. Certain point values are given to warehouses based on actions taken by users. For example, a user adding a review gives a higher point value than a user clicking on a warehouse. This enhancement shows my ability to use multiple databases and design systems that prioritize performance.

This enhancement aligns with all of the outcomes listed for the course. No changes to my original outcome plan were necessary. I learned how in memory data stores work, and their application for quick data retrieval. One challenge was managing asynchronous functions between Redis and MongoDB. Overall, this process strengthened my understanding of database integration and showed the importance of choosing the right tools to improve a system.

<img width="1249" height="501" alt="Screenshot 2026-02-10 at 5 16 03 PM" src="https://github.com/user-attachments/assets/71bd213b-0d46-4457-ae91-5980f0166f62" />

```
const getActiveWarehouses = async (req, res, next) => {

    try {

        response = []
        facilityIDs = await redisClient.zRange('activity_score', 0, 4, {REV : true})
        
        for (const id of facilityIDs){
            wh = await Warehouse.findById(id)
                .lean()
            if(!wh){
                throw new Error('failed to find facility')
            }
            response.push(wh)
        }


        for (let warehouse of response) {
            if (warehouse.photos) {
                warehouse.photoURLs = await generateImageURL(warehouse.photos)
            }
            else {
                warehouse.photoURLs = []
            }
        }

        res.json(response)
    }
    catch(err){
        next(err)
    }
} 
```

---

# Security and Design Considerations

Across all enhancements, I applied a security focused approach by implementing role based logic, validating data at the API, and ensuring that user permissions were enforced consistently. Security considerations were integrated into the design process rather than added after implementation.

---

# Summary

Each enhancement builds on the same core system while demonstrating growth across different domains of computer science. Together, they reflect my ability to iteratively improve a full stack application while balancing performance and usability.
