---
layout: post
title:  "Some Thoughts about being a Newbie"
categories: "design"
---

I'm starting on a new Project in the [Tanzu](https://tanzu.vmware.com/tanzu){:target="_blank"} team. Because I'm totally new to Container and Kubernetes technology, I have a lot to learn and think about. Following two questions keep popping up in my head.

### What makes me a newbie?

I'm a newbie because I don't know some super easy stuff. It's not any complex problems which puzzle me, on the contrary, it's always the simplest fundamental knowledge which makes me scratch my head all day long. I will give you an example. It puzzled me for a whole day but solved by my teammate's one sentence.

It's about copying a new file to the container and commit it to the image.

```bash
# List the docker containers and find the container id
docker ps
# Copy the file to the container
docker cp <your new file> $containerID:/usr/local/bin/
# Commit the container to the image
docker commit $containerID <image name>
# Restart the container
docker restart $containerID
```

My problem is after I restarted the container, the container was down. It's because I forgot to make the file executable before I copied it to the container. Therefore the container couldn't run after restart. Since the container was down, when I run "docker ps", I could not find the container anymore.

What should I do? I Googled all day about how to copy a file into the docker image in the same way as I copy a file into the file system. But it turned out docker is not storing files in the same way as file system does. Later in the standup, I raised that question. One of my teammates mentioned that even when a container is dead, you can still find it by running:

```bash
docker ps -a
```

Thus, I'm still able to copy the file to a dead container in the same way as copying it to a running container as above. 

Haha, I don't even know when a container is dead, it's still there in docker. I thought a container is like a process, when it's down, it's gone.

See, a newbie like me is always puzzled by the simplest questions. And we know our questions might be silly, we are more restrained from asking teammates for help. Now you know why Stack Overflow is so popular. You can ask some super easy silly questions on Stack Overflow, which you will never ask your teammates. 

But on the other hand, it also points out the importance of a collaborative team culture. If a team is like, when anyone met a problem, he could simply call out on Slack, "Hey, there I met a problem. Could anyone give me a hand?" "Sure, buddy I have some time right now. Let's take a look together." The team will definitely grow more quickly.

### What makes a good documentation?

In the team, there are a lot of documents. Why don't the documents help a newbie like me?

I find we developers are data experts. We are good at listing data and facts. But the thing is unstructured data is of little help. It doesn't answer any questions or solve any problems explicitly.

It reminds me how do we developers usually communicate with our customers. For a developer, we might say our product has this, this and this, blabla feature. But to a customer, he might ask I don't care, just tell me how could I solve my problem. A developer might be angry hearing that, because I've given you this, this and this, why can't you solve your damn little problem? But when I become a customer, I feel the same way. I don't care about what do you have. All I care about is how it might help me with my problems.

I realized how important it is to organize our data and documents around problems and questions, rather than merely listing them one by one. It applies to our development as well. When we develop a new feature, we should ask about which user scenario it applies to? How it could help our customers solve a specific problem? Otherwise, a thousand new features are meanless at all.