---
layout: post
title: Advent of Code 2020 - Problem 6
categories: [ rust ]
---

Problem 6 of AOC 2020.

---

Part 1 asks us to find the number of unique characters in each entry.
Once again, entries are groups of lines delimited by an empty line.

This is a fairly straightforward use of a `HashSet`. On each entry,
we'll add each non-newline char to the set, then return the length.
Sum these up and we're done with part 1.

{% highlight rust %}
fn problem_6_a(filename: &str) -> usize {
    let text = common::get_text(filename).unwrap();
    let entries: Vec<_> = text.split("\n\n").collect();
    entries.iter().map(|entry| {
        let mut h = HashSet::new();
        for c in entry.chars() {
            if c != '\n' {
                h.insert(c);
            }
        }

        h.len()
    }).sum()
}
{% endhighlight %}

Part 2 asks us to find the number of characters present in each line
of an entry, then sum those results.

All we need to do is change the `HashSet` to a `HashMap` and keep
track of counts for each char as we go through the lines of an entry.
After iterating through all lines in the entry, we can return the
number of chars in the map that have the same count as the number of
lines.  Sum these up and we're done with part 2.

Cool thing! Rust provides a nice `.entry(e)` interface to its
`HashMap`s that lets us insert default values.

{% highlight rust %}
fn problem_6_b(filename: &str) -> usize {
    let text = common::get_text(filename).unwrap();
    let entries: Vec<_> = text.split("\n\n").collect();
    entries.iter().map(|entry| {
        let mut h = HashMap::new();
        let answers: Vec<_> = entry.split('\n').filter(|a| { *a != "" }).collect(); // Get rid of empty line at end of file
        for ans in answers.iter() {
            for c in ans.chars() {
                *h.entry(c).or_insert(0) += 1
            }
        }
        h.iter().filter(|e| *e.1 == answers.len()).count()
    }).sum()
}
{% endhighlight %}

I'm kind of wondering if my use of iterators is idiomatic or not...
