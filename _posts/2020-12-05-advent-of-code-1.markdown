---
layout: post
title: Advent of Code 2020 - Problem 1
categories: [ rust ]
---

I'll be blogging about my solutions to AOC 2020 here.  This is problem 1!

---


## Utils

First of all, some utilities! Some problems call for separating out
lines while others just need the whole thing as a string.

{% highlight rust %}
use std::fs::File;
use std::io::prelude::*;
use std::io::BufReader;
use std::io::Result;

pub fn get_lines(filename: &str) -> Result<Vec<String>> {
    let file = File::open(filename)?;
    let br = BufReader::new(file);
    Ok(br.lines().filter_map(Result::ok).collect())
}


pub fn get_text(filename: &str) -> Result<String> {
    let mut file = File::open(filename)?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
}
{% endhighlight %}

First thoughts on part 1: this is two-sum. The naive solution is to just do a
double for loop which is O(n^2).  Knowing this, we can sort (O(n log
n)) and do linear things for an easy time save. We plop an index at
the beginning (low numbers) and at the end (high numbers) and
calculate the sum of the indexed entries.  If it's less than the
target, we increment the low number index, and if it's greater, then
we decrement the high number index.

Here we have a function to get the indices of the entries that sum to
the target:

{% highlight rust %}
fn two_sum_ixs(arr: &[isize], target: isize) -> Option<(usize, usize)> {
    let mut i = 0;
    let mut j = arr.len() - 1;
    loop {
        if arr[i] + arr[j] < target {
            i += 1;
        } else if arr[i] + arr[j] > target {
            j -= 1;
        } else {
            return Some((i, j));
        }

        if i > j {
            return None;
        }
    }
}
{% endhighlight %}

... and the actual problem.  Get input, parse ints, sort, two sum.  Multiply the output for the answer.

{% highlight rust %}
fn problem_1_a(input_file: &str) {
    let lines = common::get_lines(input_file).unwrap();

    let mut int_input: Vec<isize> = lines.iter().map(|s| {
        str::parse::<isize>(s).unwrap()
    }).collect();
    
    int_input.sort();
    if let Some((i, j)) = two_sum_ixs(&int_input, 2020) {
        let a = int_input[i];
        let b = int_input[j];
        println!("{}", a * b);
    }
}
{% endhighlight %}

Part 2 of this problem is obviously three-sum! Same deal here, but
this time we have three indices, two at the front, and one at the
back.  We move the second and last as in two-sum, but if they cross
over each other, we increment the first and try again.  Ultimately
this comes out to O(n^2) time which is a time save over the O(n^3)
naive solution.

{% highlight rust %}
fn three_sum_ixs(arr: &[isize], target: isize) -> Option<(usize, usize, usize)> {
    let mut i = 0;
    let mut j = 1;
    let mut k = arr.len() - 1;
    loop {
        let sum = arr[i] + arr[j] + arr[k];
        if j >= k {
            i += 1;
            j = i + 1;
            k = arr.len() - 1;
        } else {
            if sum < target {
                j += 1;
            } else if sum > target {
                k -= 1;
            } else {
                return Some((i, j, k));
            }
        }
        if i >= arr.len() - 2 {
            return None
        }
    }
}


fn problem_1_b(input_file: &str) {
    let lines = common::get_lines(input_file).unwrap();

    let mut int_input: Vec<isize> = lines.iter().map(|s| str::parse::<isize>(s).unwrap()).collect();

    int_input.sort();
    if let Some((i, j, k)) = three_sum_ixs(&int_input, 2020) {
        let a = int_input[i];
        let b = int_input[j];
        let c = int_input[k];
        println!("{}", a * b * c);
    }
}
{% endhighlight %}

Easy interview question here, all in all.
