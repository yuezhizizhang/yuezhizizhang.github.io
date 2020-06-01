---
layout: post
title:  "Some Thoughts about being a Newbie"
categories: "design"
---

I'm starting a new Project in the [Tanzu](https://tanzu.vmware.com/tanzu){:target="_blank"} team. Because I'm totally new to Container and Kubernetes technology, I have a lot to learn and think about. Following two questions keep popping up in my head.

### What makes me a newbie?

I'm a newbie because I don't know some super easy stuff. It's not some complex problems which puzzle me, on the contrary, it's always the simplest fundamental knowledge which makes me scratch my head all day long. I will give you an example. It puzzled me for a whole day but solved by my teammate's one sentence.

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

My problem is after I restarted the container, the container was down. It's because I forgot to make the file executable before I copied it to the container. Thus the container couldn't run after restart. Since the container was down, when I run "docker ps", I could not find the container anymore.

What should I do? I Googled all day about how to copy a file into the docker image in the same way as I copy a file into the file system. But it turned out docker is not storing files in the same way as file system does. Later in the standup, I raised that question. One of my teammates mentioned that even when a container is dead, you can still find it by running:

```bash
docker ps -a
```

Therefore, I'm still able to copy the file to a dead container in the same way as copying it to a running container as above. 

Haha, I don't even know when a container is dead, it's still there in docker. I thought a container is like a process, when it's down, it's gone.

See, a newbie like me is always puzzled by the simplest questions. And you know your question might be silly, you don't want to ask others even more. Now you know why Stack Overflow is so popular. You can ask some super simple silly questions on Stack Overflow, which you will never ask your teammates.

### What makes a good documentation?

In the team, there are a lot of documents. Why don't the documents help a newbie like me?

I find developers are good at listing points, but are poor at answering questions. The documents are not helpful at all because they are merely a pile of facts. As a newbie, I'm not able to find a clue in the facts to solve my specific questions.

It reminds me how do we developers usually communicate with our customers. For a developer, we might say our product has this, this and this, blabla feature. But to a customer, he might ask I don't care, just tell me how could I solve my problem. The developer might be angry hearing that, because I've given you this, this and this, why can't you solve your damn little problem?

But when I become a customer, I really don't care about what do you have. All I care about is how it might help me with my problems. I start realizing how important to organize our documents around questions and problems, rather than listing facts. It applies to our development as well. When we develop a new feature, we should ask about which user scenario it applies to? How it could help our customers solve a specific problem? Only in that way, could we develop a useful product.