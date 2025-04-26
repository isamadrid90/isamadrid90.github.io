---
title: "Bye Bye WIP commit messages"
description: "Bye Bye WIP commit messages"
publishDate: "9 July 2023"
cuid: cljv9hll5000309jqbmqy3jn3
slug: bye-bye-wip-commits
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ylveRpZ8L1s/upload/891d83910c4b3ff03466c8f79d547f96.jpeg
tags: ["software-development", "intellij-idea", "intellij", "ai-tools", "aitools"]

---

After many years of working in teams and developing complex projects, I am very aware of how important it is for my commits to contain the necessary information to understand the change I have made for some reasons:

1. They help us understand what we intend to achieve with the lines we have added, modified, or deleted, which facilitates code review and allows us to provide more effective feedback.

2. They serve as a form of documentation, helping us understand the changes that have been introduced. If we evaluate them together with past commits, they help us get an idea of the context in which those decisions were made.

3. They are crucial when we need to do a rollback.


There are many more reasons, but I don't want to go too deep into this topic because there are people who know more than me and have been talking about this for years.

I believe that as we move more towards asynchronous work and use techniques like Trunk Based Development, commits become more important. But to be honest, there are occasions when they are the last thing I worry about. If something is failing in production, my commit will likely be something like "Fix X failing," and I'm content with that 😎. Or if we are doing pair programming and changing the driver, my commit might be just "WIP." Sometimes, when I'm frustrated trying to make something work, I might write "Another try to make it work," and the truth is, this is not very useful for the future or provides any context.

## JetBrains AI Assistant to the rescue

Maybe people who have been working with Copilot or another AI-powered assistant for some time won't be surprised by this, but in my case, my interaction with AI is based on asking questions to ChatGPT, trying them out, and repeating the process until what it tells me works or improving the texts I write.

But let's get to the point. In the latest EAP version of IntelliJ Ultimate, an assistant has been incorporated that could put an end to my 💩 commits, and using it is very simple, especially if you're used to using the IDE to make commits (which is not my case—I like to use the terminal—but I'm changing and renewing myself 💁‍♀️).

Here's how to use it:

1. Access the Commit section of your IDEA. In my case, I can use the shortcuts Cmd+0 or Cmd+k.

   ![Screenshot of IntelliJ IDEA showing how to access the Commit section through the side menu](https://cdn.hashnode.com/res/hashnode/image/upload/v1688892312759/8af4cb47-e4a3-4dd8-aa8b-22bf43469308.png align="left")

2. Once there, select the files you want to commit. It's important to note that it's best to commit small changes incrementally. This way, the commit messages will be more complete.

   ![Screenshot of the Commit section of IntelliJ IDEA highlighting the area with all the modified files](https://cdn.hashnode.com/res/hashnode/image/upload/v1688892508698/aa1f61a0-4a80-4353-b197-283628deff79.png align="left")

3. To write the commit message using the assistant, click on the purple stars above the area where you would write the message.

   ![Screenshot of the Commit section of IntelliJ IDEA highlighting the purple stars to use the assistant when writing commit messages](https://cdn.hashnode.com/res/hashnode/image/upload/v1688892680263/5ed6efca-4b89-4f9b-ae99-40acdcb69765.png align="left")

4. If it's your first time using the assistant, it will ask you to log in, and *voilà!* You have your detailed commit message. You can find a link below to see the explained example.


%[https://youtube.com/shorts/ByXYat8i7B0] 

You can find more information about the IntelliJ assistant [on their blog](https://blog.jetbrains.com/idea/2023/06/ai-assistant-in-jetbrains-ides/).
