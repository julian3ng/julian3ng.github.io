---
layout: post
title: Advent of Code 2020 - Problem 4
categories: [ rust ]
---

Problem 4 of AOC 2020.

---

This one took me a while because of some nasty regex business.

Part 1: this is the first time we'll be reading in all the data in one
string.  We do this because each entry can be spread across several
lines, and entries are delimited by an empty line.

So, we just get the text and split on `\n\n` to get all the entries.

After that, we need to find the entries that have all the fields, with
the exception of `cid`.

To do this, I chose to have a vector of all seven of the patterns we
need to match, then iterated over the entries and kept the ones that
matched all seven.

{% highlight rust %}
fn problem_4_a(filename: &str) -> usize {
    let text = common::get_text(filename).unwrap();
    let entries: Vec<_> = text.split("\n\n").collect();
    let field_regexes = vec![
        Regex::new(r"(byr:.+)").unwrap(),
        Regex::new(r"(iyr:.+)").unwrap(),
        Regex::new(r"(eyr:.+)").unwrap(),
        Regex::new(r"(hgt:.+)").unwrap(),
        Regex::new(r"(hcl:.+)").unwrap(),
        Regex::new(r"(ecl:.+)").unwrap(),
        Regex::new(r"(pid:.+)").unwrap(),
    ];

    entries.iter().filter(|entry| {
        field_regexes.iter().filter(|rx| {rx.is_match(entry)}).count() == 7
    }).count()
}
{% endhighlight %}

Part 2 is similar but with conditions on the fields this time.  

{% highlight rust %}
fn problem_4_b(filename: &str) -> usize {
    let text = common::get_text(filename).unwrap();
    let entries: Vec<_> = text.split("\n\n").collect();
    let field_regexes = vec![
        Regex::new(r"byr:(19[2-9][0-9]|200[0-2])\b").unwrap(),
        Regex::new(r"iyr:(20(1[0-9]|20))\b").unwrap(),
        Regex::new(r"eyr:(20(2[0-9]|30))\b").unwrap(),
        Regex::new(r"hgt:(1[5-8][0-9]|19[0-3])cm|(59|6[0-9]|7[0-6])in\b").unwrap(),
        Regex::new(r"hcl:(#[0-9a-fA-F]{6})\b").unwrap(),
        Regex::new(r"ecl:(amb|blu|brn|gry|grn|hzl|oth)\b").unwrap(),
        Regex::new(r"pid:([0-9]{9})\b").unwrap(),
    ];

    entries.iter().filter(|entry| {
        let entry = entry;
        let c = field_regexes.iter().filter(|rx| {
            rx.is_match(entry)
        }).count();
        c == 7
    }).count()
}
{% endhighlight %}

I was overshooting my answer for a while here before I added the word
boundary pattern (`\b`) to the end of my regexes here.  The problem
was that something like `byr:(19[2-9][0-9]|200[0-2])` without the
boundary will match `byr:1920` AND ALSO `byr:1920000000000`. Adding
this pattern fixed the issue, solving part 2.
