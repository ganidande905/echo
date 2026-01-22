+++
title = "Building My Own Server Architecture"
date = 2026-01-22T16:41:53+05:30
draft = false
slug = "building-my-own-server-architecture"
tags = [
  "self-hosting",
  "docker",
  "containers",
  "reverse-proxy",
  "traefik",
  "cloudflare",
  "networking",
  "server-architecture",
  "devops-journey",
  "learning-in-public"
]
categories = ["infrastructure"]
description = "From knowing nothing about servers to designing and running my own containerized, monitored, and scalable server architecture."
+++

There was a time when the word **server** itself felt intimidating to me. I knew how to write code. I could build applications. I could make things work locally. But the moment someone said *deployment*, *hosting*, or *infrastructure*, my confidence dropped.

This blog is about how I went from knowing **nothing** about servers to designing and running my **own server architecture**  hosting multiple projects, managing traffic, security, monitoring, and slowly understanding what real-world engineering actually looks like. This journey didn't come from courses or perfectly structured tutorials. It came from breaking things repeatedly and sitting with the mess until I understood it.

## The Beginning: Zero Knowledge, Pure Curiosity

I didn't start with a plan to learn DevOps or infrastructure. It started through my **club exposure**. I saw seniors hosting their own projects, deploying independently, and talking about servers like it was normal. That's when a thought hit me: if I'm building real projects, why am I depending on platforms I don't fully understand?

So I decided to host my own projects. Not on managed platforms. On my own server. At that point, I didn't even know what that fully meant.

## First Attempt: Raw Server, Exposed Ports, Pain Everywhere

I started with a **cloud VM** — free tier, limited CPU, limited RAM. My first deployment approach was very naive: clone the code directly onto the server, run it manually, expose a port, and access it via IP and port. It worked, but barely.

Every change was painful. A code change meant SSH, stopping processes, pulling changes, and restarting. One crash meant the entire app went down. Multiple projects meant multiple exposed ports. There was no isolation, no scalability, no structure. At that time, I didn't even realize how bad this setup was. I only knew that it felt wrong and fragile.

## Discovering Docker: The First Real Breakthrough

That's when I came across **Docker**. Initially, it felt unnecessary. Why add another layer when things already worked? But once I understood containers, everything clicked. Docker gave me isolation between projects, consistent environments, easy restarts, predictable deployments, and a clean, repeatable structure.

I dockerized **every single project**. Each app became one container with one responsibility and one predictable runtime. This was the first time my server felt intentional instead of accidental.

## Reality Check: 1GB RAM Is Not a Joke

Free cloud servers don't care about enthusiasm. They give you **1GB RAM** and expect you to survive. Running multiple containers, databases, a reverse proxy, and monitoring tools on 1GB RAM forces discipline. I had to learn what actually consumes memory, how to limit container resources, how to remove unnecessary services, and how to optimize instead of blindly scaling.

This phase taught me something important: infrastructure is not about adding resources. It's about respecting limitations.

## Networking Hell: Ports, Firewalls, and Confusion

Initially, everything was exposed: app one on one port, app two on another, database on yet another. It was insecure, messy, and unscalable. After endless Stack Overflow searches and trial-and-error, I realized something simple: I shouldn't be exposing ports at all.

So I moved to internal Docker networking, minimal public exposure, and a single shared database. Everything communicated internally. Only the reverse proxy faced the outside world.

## Reverse Proxy Decisions: Nginx vs Traefik

Once ports were under control, routing became the next problem. I had two choices: Nginx or Traefik. I chose **Traefik** because it understood Docker natively with automatic container discovery, label-based routing, easier HTTPS handling, and less manual configuration. With Traefik, containers declared how they should be exposed, and the proxy handled the rest. This is when my setup started feeling professional.

## Adding Cloudflare DNS: Security That Broke Everything

Wanting an extra layer of security, I added **Cloudflare DNS**. Everything broke. SSL issues, routing failures, timeouts — nothing worked. After hours of debugging, I found a random Reddit post that explained the exact issue I was facing. One configuration change fixed everything. That moment taught me an important lesson: security layers amplify mistakes, but they also accelerate learning.

## College WiFi vs SSH: An Unexpected Blocker

Just when things stabilized, another problem appeared. My **college WiFi blocks port 22**, which meant no SSH access, no debugging, and no control over my server. That's when I discovered **sslh**, a protocol demultiplexer. The idea was simple: SSH and HTTPS share port 443, and sslh routes traffic based on protocol. Brilliant in theory, but disastrous in practice.

## The Inception Moment: Docker Ports vs VM Ports

Setting up sslh broke my web apps completely. The reason was subtle: sslh used port 443, Traefik used port 443, Docker had its own port mappings, and the VM had its own exposed ports. For weeks, everything felt confusing.

Until one realization hit: Docker ports and VM ports are not the same thing. Once that mental model clicked, everything made sense. After restructuring, sslh handled external traffic, Traefik handled internal routing, containers stayed isolated, and there were no port conflicts. It finally worked.

## Comfort and Visibility: Dashboards and Monitoring

SSH-ing into the server for everything felt inefficient. So I added Portainer for container management, Traefik dashboard for routing visibility, and Uptime Kuma for monitoring. Now I could restart containers from a UI, see routing status, monitor uptime, and manage infrastructure without SSH every time. This is when the server stopped feeling fragile and started feeling alive.

## Thinking Bigger: Discovering Docker Swarm

Once stability was achieved, my questions changed. Instead of asking whether something would work, I started asking what if a container crashes, what if traffic increases, and what if I need multiple instances? That's when I came across **Docker Swarm**.

Swarm felt approachable because it extended concepts I already knew: nodes, services instead of containers, replicas, and overlay networks. The biggest shift was this idea: I don't care where a container runs. I only care that it runs. Even though I didn't fully move my setup to Swarm, it changed how I thought about deployment and scalability.

## Kubernetes: The Next Mountain

Eventually, everything pointed towards Kubernetes.
At first glance, it felt overwhelming. Pods, services, ingress, config maps, secrets, deployments, stateful sets. It did not feel like something you casually pick up in a weekend.
But this time, I was not intimidated.
Because by now, I understood containers. I understood networking. I understood reverse proxies. I understood failures and constraints.
Even though I have not moved my setup to Kubernetes yet, it no longer feels like an abstract buzzword. It feels like a structured solution to problems I have already faced manually.
Kubernetes is not something I am rushing into. I want to approach it with intent. To understand why each abstraction exists, not just how to write YAML files.
It is the next mountain I plan to climb.

## The Hard Truth: Free Servers Don't Scale Forever

Even after optimizing everything, one truth remained: 1GB RAM is not future-proof. As more projects come in, reliability starts dropping. That's when I decided I need my own server, something scalable, and something I fully control. I'm currently on that path.

## What This Journey Taught Me

This journey taught me more than tools. It taught me how to think: clean architecture applies to infrastructure too, infrastructure decisions shape application design, small mistakes compound quickly in production, debugging infra builds patience like nothing else, and fundamentals beat copy-paste configurations.

Today, I can confidently say that I know how to design, deploy, and maintain servers across different technologies and scales. And the best part? I started knowing nothing.

## Final Thought

This was not a tutorial-driven journey. It was a problem-driven one. That's why it stuck. If servers scare you, that's a good sign. It means there's growth waiting on the other side. I'm glad I crossed it.

