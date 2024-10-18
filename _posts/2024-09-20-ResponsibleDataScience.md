---
layout: post
title:  "Responsible Data Science"
author: StevenH
categories: [Blog]
tags: [AI, Data Ethics, Data Governnance, Machine Learning]
image: assets/images/post-imgs/2024-09-20-ResponsibleDataScience/writing-stock-image.jpg
description: "The importance of good governance in data science project work"
featured: true
hidden: true
---

*Image Source: Image by <a href="https://unsplash.com/@homajob?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Scott Graham</a> on <a href="https://unsplash.com/photos/man-writing-on-paper-OQMZwNd3ThU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash">Unsplash</a>


  

Our Data Science Unit blog pages are primarily a space for us to share technical detail and progress on our project work. However, we think it is a good opportunity to highlight how we work responsibly and ethically with data and experimental data science techniques. 

Over the last few years, the Data Science Unit have had the pleasure to deliver lots of interesting projects that explore the use of data science tools and techniques such as Machine Learning (ML) and Artificial Intelligence within Welsh Government. Much of this work is experimental and aims to test novel algorithms or use untraditional or new data that comes with certain risks. It is crucial that project work is initiated and progressed with a good understanding of relevant risks.

To identify and manage these risks, the Data Science Unit considered a range of frameworks and standards as potential options to embed in our project processes. Engaging with these enables us to confidently deliver data science solutions with appropriate governance in place. This blog will cover some of the reasons why governance is important and our experience with the frameworks that we have adopted as part of our project process. 

## The benefits of having good governance mechanisms

Good quality governance can strengthen a project in multiple ways. Reasons to include governance mechanisms could stem from legal requirements or ensuring that we are being ethical when working with data or testing new techniques. Here are some of the key benefits of having robust governance mechanisms in place.
 1. **Trust in Data**

Data is the backbone of all data science projects. Ensuring the quality, accuracy, and integrity of data is fundamental and not considering these can significantly limit the success of a project. Good governance practices also involve understanding circumstances around the data such as collection, storage, and processing. 
2. **Confidence in machine learning and AI**

Machine learning models have been around for quite some time and the risks of using these algorithms are well documented. Limitations such as interpretability, performance, generalisation and bias all potentially hinder the adoption of ML and lower confidence in the usefulness of these algorithms. Good governance aims to identify and address these limitations to ensure that algorithms and their potential uses are well understood, quality is measured and transparent, and that assumptions and issues are well documented. 
3. **Privacy and GDPR Compliance**

The General Data Protection Regulation (GDPR) has clearly defined expectations around what data can be used and for what purposes. Having the right governance procedure can ensure that data is used in a way that complies with legal requirements and protects the privacy of individuals. For organisations, another benefit is that non-compliant actions are mitigated, potentially avoiding financial penalties and upholding the reputation of the organisation. 
4. **Ethics**

With the constant improvement in performance of machine learning models and the ease at which they can be deployed, it can be easy to get carried away with these fascinating algorithms and not properly reflect on the ethics involved. Being able to use data to train a model to a high accuracy and deploy it for something useful does not mean that it is right to do. Ethical governance can help us consider the wider implications and consequences of a proposed project and ensure that we are working responsibly.
5. **Transparency**

Transparency is an important element to governance and reaffirms the points above. Being open and transparent is key to responsible data science and helps build trust in project work and those developing data science solutions. This openness gives stakeholders, including the public, a chance to learn more about our work, understand how data science is implemented and how conclusions are drawn. Ensuring that project work is open and honest in all aspects of the project is fundamental to establishing a professional culture and greater maturity as an organisation.
6. **Understanding Risks and Blockers**

Governance can also help identify and mitigate risks in project work and improve the chances of a successful outcome. This involves recognizing potential blockers that could hinder data projects such as data quality issues, ethical concerns, or technical limitations. By understanding these risks early on, we can develop strategies to address them proactively or identify that the project is not feasible and avoid wasting resource.

This list of benefits is not exhaustive but demonstrates how governance frameworks can add value to data science projects. In the Data Science Unit, we use 3 tools to assess project work against and give an overview of these below.

### Data Protection Impact Assessment

The Data Protection Impact Assessment (DPIA) aims to understand if the data used in a project contains any personal data. Specifically, it assesses the privacy implications of collecting, securely storing, sharing and managing data that has personal information.
The assessment first considers whether the data holds any personal information including [special category information](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/lawful-basis/special-category-data/what-is-special-category-data/) and if so,  asks further questions about: 

*	the collection of the data
*	the processing and storage of the data
*	the persistence of the processing
*	the legal basis for working with the data
*	the visibility of key roles (e.g.  data controllers, data processors)
*	data retention periods

Regardless of whether the assessment finds that the project will involve any personal information, the mandatory fields of the form are filled in and saved as a record of completion. This process is valuable in identifying data privacy issues early on. If personal data is identified, we can discuss further with our data protection experts to better understand what additional mitigations need to be in place or what advice (legal for example) we need to obtain to ensure that we are compliant. 

The data protection impact assessment was the first piece of governance we adopted into our project process and has been used throughout Welsh Government for some time. Several projects have led to further discussions with data protection officers that have helped to address potential issues and also support us to define some elements more clearly giving assurance that our activities are compliant and we are using data appropriately.

### Government Data Ethics Framework
The [Data Ethics Framework](https://www.gov.uk/government/publications/data-ethics-framework) published by the Central Digital and Data Office is the tool that we have adopted in to our governance process that focusses on the ethics of our work. It is also the most collaborative framework that we complete, usually involving a meeting between several members of the team to discuss the answers we provide. We have found this useful and often get a mix of perspectives which helps us reflect on our own awareness of potential issues.

The form aims to record ethical considerations and capture any risks on a project that can subsequently be investigated and, where possible, mitigated. The framework is broad and extensive, asking a range of questions that don’t always apply to each individual project. Questions are designed to uncover potential ethical risks with use of data, use of algorithms or publication/deployment of project deliverables. The framework is also designed to be revisited over the lifespan of the project and updated as things progress recognising that things can change. Appreciating that this framework acts as a living document early on is important as toward the start of a project there will be parts that can’t be completed yet, and there may be other parts of the framework that need to be updated to reflect changes during a project lifecycle.

The ethics framework challenges the project team to properly define the expected benefit of the project, consider whether expertise on the project is diverse and appropriate and also that the planned activities comply with the law and have appropriate governance mechanisms set up. The framework tests the quality of the data, records any limitations and prompts the project team to think about how models and results can be transparent, offering an opportunity for scrutiny. The framework ends with consideration of the wider policy implications and other factors that are relevant once the project has been deployed or the outputs delivered such as skills and training for potential users.

Having an extensive framework to capture ethical risks has enabled us to confidently engage in project work with a greater awareness of our responsibilities. Taking time to discuss and revisit the questions with the project team is productive and prompts some good discussions.

### Algorithmic Transparency Recording Standard 


Finally, the newest edition to our governance process is the [Algorithmic Transparency Recording Standard](https://www.gov.uk/government/collections/algorithmic-transparency-recording-standard-hub) (ATRS) produced by the Department for Science, Innovation and Technology (DSIT).  The ATRS is an incredibly useful framework through which information about the development and use of algorithms can be captured and shared publicly. The records present a great opportunity to fully engage with our commitment to work transparently with both the public and peers at multiple levels of detail. The ATRS was formally launched in September 2024, and we were really pleased to be contributing to the register early in its existence to help demonstrate the value exposing details of the algorithms we develop or work with.

The ATRS template is split in to two tiers, the first tier requires non-technical information about the algorithm and the second tier is for the technical detail. The first tier is designed so that a description of the algorithm, what it does and why it is being used, is presented in non-technical language and can be understood by anyone who is interested. The second tier   discusses the model, data, risks and ethics in more detail. 

We hope that overtime more organisations across the Welsh public sector will engage with the ATRS. We see it as a great opportunity to share details about how we use algorithms including ML and AI and that it builds a greater level of transparency. We also hope that engagement with the ATRS will increase opportunities for collaboration with others who may be working on similar projects across the public sector.


### Reflection on Governance

Being open and honest about the way that we are developing and using algorithms is more important than ever after the recent surge in AI, particularly generative models and their difficulty to quantitatively quality assure compared to traditional machine learning models.  The governance we have adopted helps us ensure that we are being responsible and transparent in our activities and that we welcome discussion. If you are thinking about introducing or revising your current governance processes, then I’ll end this blog with some learning points and observations that we have made while using these frameworks.

They are broad, designed to apply to a range of different project types involving data. This inevitably means that there will be parts of the frameworks that don’t apply to the project you are reviewing. Having completed several projects we are comfortable with skipping over the sections that don’t apply. All three of the frameworks detailed above are treated like living documents and are reviewed intermittently and updated when appropriate. This also gives us an opportunity to revisit questions later in a project’s lifespan where they may be more relevant. 

We have found completing these frameworks as a team useful, particularly with the ethics framework, a discussion can uncover issues that may have been missed by one individual. It also reinforces the responsible culture we are striving to cultivate and raising awareness of issues and considerations through the team. It’s also an opportunity for junior staff to think more holistically about the project process and understand issues or learn more about mitigations and solutions.

The DPIA and ethics framework specifically presents an opportunity to discuss issues with our internal experts on data privacy and data protection. These discussions have been invaluable and let us develop a better understanding of risks and that we don’t engage in work that is non-compliant. Having this assurance from experts let us confidently progress our project work.

Having these frameworks as part of our governance process also gives us consistent templates to complete and catalogue details about the projects we work on. Having these familiar templates also makes it easier to identify and discuss common risks or issues that occur across projects.


### Data Science in Welsh Government

The Data Science Unit are continuously developing and engaging in more novel data science project work that comes with greater risks. The frameworks we have discussed above give us assurance that we are being responsible and transparent in the work that we do. As a government data science team we should be open to challenge and discussion in how we are using data and algorithms, especially when the data might have personal information, or the algorithm might be experimental. Engaging with good governance mechanisms is a step that any organisation can take to aid in being more open and welcoming discussion.
 





 



 







