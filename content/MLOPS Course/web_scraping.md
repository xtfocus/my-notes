---
title: MLOPs Course Project
tags: ["mlops"]
categories: ["mlops"]
---

<p align="center">
  <img src="attachments/hanoi_weather.png" alt="hanoi_weather.png"/>
</p>



This post is a dashboad of what I'm working on for the final project of [[Quan_course_introduction|Quan's course]]. I will be updating frequently

# The project idea

- A simple weather monitoring & prediction application 
- LLM for activities recommendation

It's dead simple. The only "ops" thing here is the automation.

Here I focus on writing nice code rather than doing fancy stuff.

# Crawling

https://github.com/xtfocus/weather_data_crawler

Good old soup + requests combo is enough.

Features:

- query historical weather data (WIP)
- query current weather data (DONE)
- query forecasting results provided by third parties (usually the websites themselves) (WIP)

The repo also features some good dev practices:
- tests
- pre-commit hooks
- docstrings
- Pydantic models

# Prediction

A small tree model to forecast weather

Features: model registry, serving, monitoring, continuous training 


# LLM recommendation for activities

Something like a travel assistant agent.
