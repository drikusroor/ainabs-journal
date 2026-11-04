---
title: "A first encounter with ChatGPT"
date: 2022-12-04T20:41:00Z
categories:
  - "genuine-post"
tags:
  - "ai"
  - "git"
source: Notion
featured_image: "https://www.notion.so/images/page-cover/gradients_10.jpg"
---

The arrival of DALL-E 2, Stable Diffusion, and GPT-3 has opened up a new world for developers, data scientists, and other tech adepts. These powerful AI models are capable of generating images, text, and other data in a wide range of styles and formats, and they offer exciting new possibilities for a variety of applications.

One of the most exciting developments in this field is ChatGPT, an AI model that is trained to generate conversational text in a natural and engaging way. ChatGPT is based on the GPT-3 model, and it is specifically designed for use in chatbots and other conversational AI applications.

What sets ChatGPT apart from other AI models is its ability to generate responses that are relevant, coherent, and appropriate to the context of the conversation. This makes it an ideal tool for building chatbots that can engage with users in a natural and engaging way, and it opens up exciting new possibilities for applications in fields such as customer service, e-commerce, and social media.

In fact, the introduction you just read has been entirely written by ChatGPT. I am a slow writer and I didn’t want to spend 30 minutes on a text that would probably not have been better than ChatGPT’s introduction.

ChatGPT works like any other chatbot. You ask it a question, perhaps with some context, and it will give you an answer.

## ChatGPT to generate commit messages

In this post, I would like to share my experience using ChatGPT to generate commit messages for my Git repository. As a developer, I am always looking for ways to automate and streamline my workflow, and I was excited to try out ChatGPT as a tool for generating commit messages.

I was impressed by the ability of ChatGPT to understand the context of my repository and generate commit messages that were relevant, coherent, and appropriate to the changes I had made. The generated commit messages were clear and concise, and they helped me to quickly and accurately describe the changes I had made to my code.

In my [update-advisors](https://github.com/drikusroor/update-advisors) repository, I use [Github Actions](https://github.com/features/actions) to periodically fetch updates in accredited financial advisors and its organizations. How this works is currently not important, but every x-amount of hours it fetches the new organizations and stores them into `organizations.json`. This file contains an array of organizations with their respective advisors. It then commits these changes to the repository with a the default commit message `Github Actions: Update advisors`.

This commit message is not very descriptive. I would like it to contain more information about which organizations have been added and / or removed.

Enter ChatGPT.

I started with me asking the following question:

> I have a repository in which the json file “organizations.json” gets updated automatically using github actions. It contains an array of objects that contains information about organizations. Every time it gets updated, I want to add the names of the organizations that have been added to the git commit messages. How do I do that?

* * *

### ChatGPT’s answer

To automatically add the names of the organizations to your git commit messages, you can use a tool called `commit-msg` which is a hook provided by git. This hook is executed every time you try to commit a change to your repository, and it allows you to automatically modify the commit message.

To use this hook, you will need to create a file called `commit-msg` in the `.git/hooks` directory of your repository. This file should contain the following code

```shell
#!/bin/sh

# read the commit message from the command line
commit_message=$1

# parse the organizations.json file and extract the names of the organizations
organization_names=$(jq '.organizations[].name' organizations.json)

# add the organization names to the commit message
echo "$commit_message" > "$commit_message"
echo "" >> "$commit_message"
echo "Organizations: $organization_names" >> "$commit_message"
```

This code will read the commit message that you have entered, parse the `organizations.json` file to extract the names of the organizations, and then add those names to the commit message before committing the changes to your repository.

Make sure to make the `commit-msg` file executable by running the following command:

```shell
chmod +x .git/hooks/commit-msg
```

After you have done this, every time you try to commit a change to your repository, the `commit-msg` hook will automatically add the names of the organizations to the commit message.

* * *

## Not perfect

This was pretty good. It’s a git commit hook that edits the commit message. However, it adds *ALL* organizations to the commit message. This is not what I wanted.

I asked:

> Yes, but I want only the names of the organizations that have been added. Probably by using git diff right?

It responded with an updated script that uses `git diff` to find the added organizations:

```shell
#!/bin/sh

# read the commit message from the command line
commit_message=$1

# use git diff to compare the current version of organizations.json with the previous version
diff=$(git diff HEAD~1 organizations.json)

# ...
```

Not bad, eh? But still not perfect. The added organizations are now on one single line in the git commit messages, e.g. `Added organizations: Organization ABC Organization XYZ`. That’s not very readable. Furthermore, the original commit message was replaced by `.git/COMMIT_EDITMSG`. I wanted ChatGPT to keep the original commit message and to move each organization to a new line:

> It works now! However, there are still things to be improved. I’ll list them below:

```
- First, the commit message now starts with ".git/COMMIT_EDITMSG". I don't want that.
- Second, I would like a new line after "Added organizations:"
```

It updated the script for me:

```shell
# ...

# parse the diff output and extract the names of the organizations that have been added
added_organization_names=$(echo "$diff" | grep '^\+' | grep '"name":' | sed 's/^\+.*"name": "\(.*\)",$/\1/')

# add the added organization names to the commit message
echo "$commit_message" | sed '1d' > "$commit_message"
echo "" >> "$commit_message"
echo "Added organizations:" >> "$commit_message"
echo "$added_organization_names" >> "$commit_message"
```

It now removed `.git/COMMIT_EDITMSG` from the commit message and it added a new line after `Added organizations:`, but this wasn’t good enough yet. I asked it the wrong questions. The original commit message still wasn’t kept as I asked it to remove `.git/...` and the organizations were still on one line because I only asked ChatGPT to add a new line after `Added organizations:`.

It took me quite some ping-pongs with ChatGPT to achieve the exact result I wanted. After that, I couldn’t find an easy way to commit the git commit hook to the repository. I therefore asked ChatGPT to convert the script to a standalone shell script that I could then execute from within the Github Actions workflow. The end result looks like this:

```shell
#!/bin/sh

# read the commit message from the command line
commit_message=$1

# use git diff to compare the current version of organizations.json with the previous version
diff=$(git diff HEAD~1 organizations.json)

# parse the diff output and extract the names of the organizations that have been added
added_organization_names=$(echo "$diff" | grep '^\+' | grep '"tblPointOfInterestName":' | sed 's/^\+.*"tblPointOfInterestName": "\(.*\)",$/\1/')

# parse the diff output and extract the names of the organizations that have been removed
removed_organization_names=$(echo "$diff" | grep '^-' | grep '"tblPointOfInterestName":' | sed 's/^-.*"tblPointOfInterestName": "\(.*\)",$/\1/')

# add the added and removed organization names to the commit message
echo "$commit_message" > "$commit_message"
echo "" >> "$commit_message"
echo "Added organizations:" >> "$commit_message"
echo "$added_organization_names" | sed 's/^/  /' | sed 's/$/, /' >> "$commit_message"
echo "" >> "$commit_message"
echo "Removed organizations:" >> "$commit_message"
echo "$removed_organization_names" | sed 's/^/  /' | sed 's/$/, /' >> "$commit_message"

# use git commit to create a new commit with the updated commit message
git commit -a -F "$commit_message"
```

I [can call this script](https://github.com/drikusroor/update-advisors/blob/ba398e85e0ac7b64896a3f772837647c2c045c0b/.github/workflows/node.js.yml#L37) with an initial commit messageto create a commit as follows:

```shell
./helpers/commit.sh "Update advisors"
```

Which results into the following commit message:

```text
Update advisors

Added organizations:
  Organization ABC,
  Organization XYZ,
Removed organizations:
  Organization 123,
  Organization 789,
```

And which is exactly what is was hoping for.

## Conclusion

ChatGPT definitely helped me to quickly create a shell script that automatically generates commit messages for me. It probably enabled me to achieve my result faster than when I would have “brute forced” it, whether with or without Google and Stackoverflow. My knowledge of shell script is by no means satisfactory.

However, it does require some fine tuning to make it return exactly what you want. This is partly due to the user (me) asking the wrong questions or an incorrect or insufficient context, but sometimes ChatGPT also forgets context from earlier questions or simply misunderstands. One might argue: “Yes, but Ainab, this is your fault as you did not provide the correct information”. But then I would like to argue that ChatGPT is supposed to be an AI tool that should be intelligent enough to decipher what I wants. If it doesn’t do this sufficiently, I might as well try to program the script myself.

That said, I did make me think of a quote, which I presume to be from Vince Liem, and which I will now probably paraphrase in a horrible way:

> This new generation of new AI tools will convert us from content creators to *context* creators.

And with that I would like to drop the mic and get out of here. 🎤👇
