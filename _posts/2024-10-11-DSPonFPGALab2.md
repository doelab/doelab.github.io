---
title:  EE3041 DSP on FPGA Big Project Lab 2 - IIR Filter
date: 2024-10-11 08:00:00 +0700
categories: [Lab Courses, EE3041 DSP on FPGA Big Project]
tags: [lab, dsponfpga, fpga, courses]      # TAG names should always be lowercase
author: link
toc: true
comments: false
description: Lab 2 for EE3041 DSP on FPGA Course at HCMUT
media_subpath: /assets/img/DSPonFPGA/Lab2/

---

# Lab 2 - IIR Filter

## Objective

The objective of this lab is to design, implement, and test an infinite impulse response (IIR) filter using FPGA technology. The goal is to gain practical experience in digital filter design and FPGA-based digital signal processing (DSP). By the end of this lab, students will understand IIR filter theory and apply these techniques in a hardware design environment.

## A Note about Collaboration

Big project is to be accomplished in groups. All work must be from your own team members.

Hints from others can be of great help, both to the hinter and the hintee.  
Thus, discussions and hints about the assignment are encouraged. However, the project must be coded and written up in groups (you may not show, nor view, any source code from other students’ groups). We may use automated tools to detect copying.

Use Emails/LMS to ask questions or come visit us (203B3) during office hours.  
We want to see you succeed, but you have to ask for help.

## Overview

In this lab, students will design an IIR filter using Verilog or SystemVerilog. The focus will be on understanding the filter design process, coding the filter architecture, and running simulations to verify its functionality. IIR filters offer a recursive filter design and are widely used for their efficiency in achieving desired frequency responses with fewer coefficients compared to FIR filters.

For those seeking high score, there is an option to synthesize the design onto an FPGA and perform real-time testing. This will allow students to demonstrate their filter implementation on hardware, further reinforcing their understanding of FPGA-based digital signal processing (DSP).

The lab includes the following steps:

- Understanding the theory behind IIR filter design.
- Implementing the IIR filter architecture using HDL.
- Running simulations to verify the filter's performance.
- Synthesizing the design on an FPGA and conducting real-time testing.

## Background

### IIR Filter

An Infinite Impulse Response (IIR) filter is a digital filter that relies on both past input and output values to produce a current output. Unlike FIR filters, which use only input values and a finite number of coefficients, IIR filters use feedback mechanisms to achieve a recursive behavior. This makes IIR filters highly efficient for certain tasks, as they can meet frequency response requirements with fewer coefficients than an FIR filter.

However, designing IIR filters comes with challenges, such as ensuring stability, especially when implemented in hardware. Special care must be taken to avoid numerical instability, as small errors or delays in feedback can cause the filter to oscillate or behave unpredictably.

Below is a general block diagram of an IIR filter structure:

![](iir.png)

IIR filters are used for tasks such as smoothing, noise reduction, and frequency band selection, often in applications where real-time signal processing is required. Their recursive nature allows them to efficiently implement low-pass, high-pass, band-pass, or band-stop filters.

## Assignment Overview

In this lab, you will design and simulate an IIR filter using Verilog or SystemVerilog. The testbench and input signal samples provided in Lab 1 will be reused to verify the IIR filter’s performance. Your primary task is to complete the IIR filter design, simulate it, and verify its functionality. You are also encouraged to use tools like MATLAB or Python to analyze the frequency components of the provided signals to guide your filter design.

For more details, refer to [Lab 1 - FIR Filter, Assignment Overview]({% post_url 2024-09-14-DSPonFPGALab1 %}#assignment-overview)

## Samples

We will reuse the assets of Lab 1 for Lab 2. For more details, refer to [Lab 1 - FIR Filter, Samples]({% post_url 2024-09-14-DSPonFPGALab1 %}#samples)

## Demonstration on FPGA

For more details, refer to [Lab 1 - FIR Filter, Demonstration on FPGA]({% post_url 2024-09-14-DSPonFPGALab1 %}#demonstration-on-fpga)

## For Credit

- **0.75 point**: Demonstrates a basic understanding of IIR filters and designs the specifications for the IIR filter.
- **0.25 point**: Differentiate between FIR and IIR filters, explain when to use FIR or IIR, and describe their typical applications.
- **2 points**: Completes the RTL design for the IIR filter, ensuring it aligns with the given specifications. These points are awarded only if you successfully simulate and verify the filter's functionality with at least one sample.
- **3 points**: Successfully simulates the IIR filter design with the provided input samples. Your analysis should explain how well the filter performs, identifying any issues in filtering and frequency response.
- **2 points**: Implements a pipelined/parallelized architecture to achieve higher operating frequency.
- **2 points**: Synthesizes the design and performs a live demo on an FPGA, demonstrating real-time performance and signal processing.
- Bonus points:
	- Fully parameterizable IIR filter
	- Demonstrates the IIR filter's performance using real-world signals (audio signal)
	- Adaptive IIR filter with dynamically updated coefficients based on signal input
	- You can propose your own idea
	- If your filter results are not optimal, you should analyze and explain why the result is insufficient, and propose a solution to improve it. Even if the filter performance is not ideal, a well-explained analysis can still earn bonus points.

## Reporting Requirements

Your report should explain your design choices, simulations, and results. Ensure it includes clear evidence of your filter's performance, such as waveforms and graphs, and a detailed analysis of any issues encountered. For more details, refer to [Lab 1 - FIR Filter, Reporting Requirements]({% post_url 2024-09-14-DSPonFPGALab1 %}#reporting-requirements)

## Frequently Asked Questions
Some of these questions are already answered in Lab 1. Please avoid repeating previously asked questions. For more details, refer to [Lab 1 - FIR Filter, Frequently Asked Questions]({% post_url 2024-09-14-DSPonFPGALab1 %}#frequently-asked-questions)

## How To Turn In Your Solution

This semester we will be using LMS, simply submit the zip file with your reports and codes.

## Demos and Late Penalty

We will have demo / presentation times on of class times on or near the due date. Since we will demo from the files in your zip, it is possible that you’ll demo on a following day.

**Define Late:** Lateness is determined by the file dates of your submission on LMS.