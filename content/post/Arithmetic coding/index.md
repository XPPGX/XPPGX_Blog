---
title: Arithmetic coding with C
subtitle: 

# Summary for listings and search engines
summary: A simple demo of arithmetic coding with C

# Link this post with a project
projects: []

# Date published
date: "2022-04-12T00:00:00Z"

# Date updated
lastmod: "2022-04-12T00:00:00Z"

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: true

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 
  focal_point: "Center"
  placement: 1
  preview_only: true

authors:
- admin

tags:
- data compression
- C
categories:
- Algorithm
---
![Algorithm](featured.jpg)
## 環境
- OS : Windows10
- IDE : Visual Studio Code
- Compiler : The GNU C++ Compiler(mingw32-gcc-g++-bin , version:9.2.0-2)

```
/*Simple Demo*/
#include <stdio.h>
#include <stdlib.h>

//#define _DEBUG_
#define STR_LEN 10
#define CAP_char 26
#define A_ASCII_CODE 65

struct prob_interval_node{
    double L;
    double H;
}typedef prob_interval_node;

void get_str_prob(char input_string[], char char_freq[], double prob[]);
void get_prob_interval(char input_string[],double prob[], prob_interval_node prob_interval[]);
double arithmetic_encode(char input_string[], double prob[], prob_interval_node prob_interval[]);
void arithmetic_decode(double code, prob_interval_node prob_interval[]);

int main(){
    char test_input_string[STR_LEN] = {'H','E','L','L','O','W','O','R','L','D'};
    char char_freq[CAP_char] = {0};
    double prob[CAP_char] = {0};
    prob_interval_node prob_interval[CAP_char];
    double code;
    get_str_prob(test_input_string,char_freq,prob);
    get_prob_interval(test_input_string,prob,prob_interval);
    code = arithmetic_encode(test_input_string,prob,prob_interval);
    arithmetic_decode(code,prob_interval);
}

void get_str_prob(char input_string[], char char_freq[], double prob[]){
    /*Count the times and probability of a character appearing in a string*/
    for(int i = 0, pos = 0 ; i < STR_LEN ; i ++){
        pos = input_string[i] - A_ASCII_CODE;
        char_freq[pos] = char_freq[pos] + 1;
    }
    #ifdef _DEBUG_
    for(int i = 0 ; i < CAP_char ; i++){
        printf("%c freq is %d\n",A_ASCII_CODE + i,char_freq[i]);
    }
    #endif
    for(int i = 0 ; i < CAP_char ; i ++){
        if(char_freq[i] > 0){
            prob[i] = (double)char_freq[i] / STR_LEN;
            #ifdef _DEBUG_
            printf("%f\n",prob[i]);
            #endif
        }
    }
}

void get_prob_interval(char input_string[],double prob[], prob_interval_node prob_interval[]){
    /*Get prob_intervals of each character*/
    for(int i = 0 ; i < CAP_char ; i ++){
        prob_interval[i].L = 0;
        prob_interval[i].H = 0;
    }
    for(int i = 0, pre_index = 0 ; i < CAP_char ; i ++){
        if(prob[i] > 0){
            prob_interval[i].L = prob_interval[pre_index].H;
            prob_interval[i].H = prob_interval[i].L + prob[i];
            pre_index = i;
        }
    }
}

double arithmetic_encode(char input_string[], double prob[], prob_interval_node prob_interval[]){
    /*Encode the input_string and output the code in double type*/
    char ch;
    double LOW = 0, HIGH = 1;
    double pre_LOW = 0, pre_HIGH = 1;
    double code;
    for(int i = 0 ; i < STR_LEN ; i ++){
        ch = input_string[i];
        LOW = pre_LOW + (pre_HIGH - pre_LOW)*prob_interval[ch-65].L;
        HIGH = pre_LOW + (pre_HIGH - pre_LOW)*prob_interval[ch-65].H;
        pre_LOW = LOW;
        pre_HIGH = HIGH;
    }
    code = (LOW + HIGH) / 2;
    return code;
}

void arithmetic_decode(double code, prob_interval_node prob_interval[]){
    /*Decode and output a string*/
    double LOW = 0, HIGH = 1;
    double pre_LOW = 0, pre_HIGH = 1;
    for(int count = 0 ; count < STR_LEN ; count ++){
        for(int i = 0 ; i < CAP_char ; i ++){
            LOW = pre_LOW + (pre_HIGH - pre_LOW)*prob_interval[i].L;
            HIGH = pre_LOW + (pre_HIGH - pre_LOW)*prob_interval[i].H;
            if(LOW <= code && HIGH > code){
                printf("%c",i+65);
                pre_LOW = LOW;
                pre_HIGH = HIGH;
                break;
            }
        }
    }
}

```