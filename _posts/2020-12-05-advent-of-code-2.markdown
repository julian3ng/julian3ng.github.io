---
layout: post
title: Advent of Code 2020 - Problem 2
categories: [ rust ]
---

Problem 2 of AOC 2020.

---

The problem: each line of a file has a password policy and a password,
formatted 'policy:password', i.e. '2-4 a: advent'.  Our goal is to
count the number of passwords following their given policy.

For part 1, the policy is that given '2-4 a' there must be between
2 and 4 'a's, inclusive, in the password.  'advent' would not count,
but 'aardvark' would.

{% highlight rust %}
fn count_chars(c: char, s: &str) -> usize {
    s.chars().filter(|cc| {cc == &c}).count()
}

fn policy_1(min: usize, max: usize, c: char, s: &str) -> bool {
    let count = count_chars(c, s);
    count >= min && count <= max
}
{% endhighlight %}

To apply these, we run through each line with a regex to capture the
min, max, char in question, and password.

{% highlight rust %}
pub fn problem_2_a(input_file: &str) -> usize {
    let contents = common::get_text(input_file).unwrap();
    let re = Regex::new(r"(\d+)-(\d+) ([a-z]): ([a-z]*)").unwrap();
    re.captures_iter(&contents).filter(|caps| {
        let min = usize::from_str(&caps[1]).unwrap();
        let max = usize::from_str(&caps[2]).unwrap();
        let c   = char::from_str(&caps[3]).unwrap();
        let pw  = &caps[4];
        policy_1(min, max, c, pw)
    }).count()
}
{% endhighlight %}

(I'm really enjoying Rust's iterators)

We can just swap out the policy to solve part 2, whose policy
requires that exactly one of the chars indexed by the given numbers is
the given char.

{% highlight rust %}
fn policy_2(first: usize, second: usize, c: char, s: &str) -> bool {
    // We know s is ascii
    let chars: Vec<_> = s.chars().collect();
    let l = chars.len();
    // first and second are 1-indexed, our string is 0-indexed
    first - 1 < l && second - 1 < l && ((chars[first - 1] == c) ^ (chars[second - 1] == c))
}
{% endhighlight %}

You may notice that I'm liberally using `.unwrap()` - I think this is
fine, since a) this is not production code, and b) we always have nice
inputs!
