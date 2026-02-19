## Sean Sweeney
### Southern New Hampshire University  
### CS 499 Capstone ePortfolio  

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

My artifact, InTheTow, allows users to leave reviews of shipping and receiving facilities. For this enhancement, I implemented a question and answer feature that allows users to ask follow up questions on reviews and enables the original reviewer to respond.

This required designing a new Question schema in my database, establishing relationships between reviews and questions, updating API routes, and integrating the feature into the React frontend using conditional rendering and state management. I also implemented role based logic to restrict who can ask and answer questions.

This enhancement demonstrates my ability to extend an existing codebase and implement functionality without disrupting current features, directly aligning with the third course outcome. It also reflects the fourth outcome through my use of industry standard tools including MongoDB, Express, and React to design a solution that improves user interaction within a real application. This enhancement also supports collaborative engagement within the application by allowing multiple users to contribute follow up to reviews. By enabling drivers to ask questions and receive clarifications, the system supports decision making based on shared experiences.

One key challenge was managing user permissions when associating questions with reviews. Addressing this required specific validation logic. This process strengthened my understanding of application and system design. Below is a picture of the finished feature, as well as a code snippet of some implementation. The full implementation can be found within the Artifact Github repository linked earlier in this portfolio.

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

For this enhancement, I designed and implemented the InTheTow Score, a scoring algorithm that summarizes facility performance using multiple inputs such as ratings, dock times, and safety reports.

The implementation required normalizing values across different scales, applying weighted averages, incorporating confidence values, and using a growth function to stabilize scores with low volume. I evaluated tradeoffs between fairness and efficiency before finalizing the design.

This enhancement directly aligns with the third course outcome by demonstrating my ability to design and evaluate computing solutions using algorithmic principles and structured reasoning. I applied mathematical techniques to produce results from multiple sources, satisfying the fourth outcome by implementing tools and methods that deliver value within an industry.

Below is a picture of the finished feature, as well as a code snippet of some implementation. The full implementation can be found within the Artifact Github repository linked earlier in this portfolio.

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

For this enhancement, I implemented an activity scoring system using Redis to track and display recently active facilities.

I used Redis sorted sets to store and retrieve activity scores while maintaining all data in MongoDB. This required designing a hybrid approach. I evaluated the tradeoffs between in memory performance and traditional database performance before finalizing the approach.

This enhancement aligns with the third and fourth course outcomes by demonstrating my ability to evaluate architectural decisions and apply tools to improve system performance. The design also supports the fifth outcome by maintaining backend validation during database operations.

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

Across all enhancements, I applied a security mindset by implementing role based access control, validating API inputs, and enforcing permissions at the backend. This directly aligns with the fifth course outcome by anticipating misuse and designing systems that avoid potential vulnerabilities.

Beyond the technical enhancements, the overall development of this portfolio reflects the first and second course outcomes. Throughout the program at SNHU, I collaborated in classes using Agile methodologies, communicated technical decisions, and adjusted documentation for different audiences. The organization of this ePortfolio demonstrate my ability to deliver professional quality communication tailored to both technical and non technical audiences.
