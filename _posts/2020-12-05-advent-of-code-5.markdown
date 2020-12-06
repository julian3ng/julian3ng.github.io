---
layout: post
title: Advent of Code 2020 - Problem 5
categories: [ rust ]
---

Problem 5 of AOC 2020.

---

I saw the words 'binary boarding' and was very frightened because I
thought I would have to do a binary tree in Rust. As far as I can tell
this is fairly complex because of the borrow checker.

It turns out this is just a simple binary representation problem.

In this problem, boarding passes specify seats via a series of 'F's
and 'B's, followed by 'R's and 'L's. An 'F' signifies being in the low
half of the section, while a 'B' signifies being in the high half, and
similarly for 'L' and 'R'.

To find the row, we can just iterate through the first 7 chars and
accumulate the number. We start with an accumulator of 0. For every
letter, we multiply by 2, and if the letter is a 'B' (corresponding to
1), we add 1.

For example, 'BBFFBFF' accumulates like so: 0, 1, 3, 6, 12, 25, 50, 100.

(While writing this post I realized that I was really enamored with
the accumulation idea when in reality I could've just replaced 'B'
with '1' and 'F' with '0' then parsed the number... but I had fun with
this so it's fine!)

After accumulating the row, we do the same with the column, then
calculate our seat id.

After that, just a quick call to `.max()` to finish part 1.

{% highlight rust %}
fn calculate_id(i: usize, j: usize) -> usize {
    i * 8 + j
}

fn calculate_pos(line: &str) -> (usize, usize) {
    let mut row: usize = 0;
    for c in line[0..7].chars() {
        row *= 2;
        if c == 'B' {
            row += 1;
        }
    }

    let mut col: usize = 0;
    for c in line[7..].chars() {
        col *= 2;
        if c == 'R' {
            col += 1;
        }
    }
    (row, col)
}

fn problem_5_a(filename: &str) -> usize {
    let lines = common::get_lines(filename).unwrap();
    lines.iter().map(|line| {
        let (i, j) = calculate_pos(line);
        calculate_id(i, j)
    }).max().unwrap()
}
{% endhighlight %}


Part 2 we need to find *our* seat - there is one gap in the seat ids.

There's a clever way to do it with a binary search for the gap, taking
advantage of the fact that whichever half has your seat has fewer
elements than the difference in the endpoints (i.e. with `arr = [1, 2,
3, 4, 5, 6, 8]`, `3 - 0 = 3 = arr[3] - arr[0]`, but `7 - 3 = 4 < arr[7] - arr[3]`).

My implementation below will obviously hang if there's no hole, but I
have every confidence that my input is nice and has an answer.

Since we're sorting anyway, linear search would be the same
asymptotically, but this is *clever*.

{% highlight rust %}
fn find_hole(arr: &[usize]) -> usize {
    let (mut i, mut j, mut k) = (0, arr.len() / 2, arr.len() - 1);
    loop {
        if i == j {
            return arr[i] + 1;
        }
        if arr[j] - arr[i] > j - i {
            k = j;
            j = i + (k - i) / 2;
        } else if arr[k] - arr[j] > k - j {
            i = j;
            j = i + (k - i) / 2;
        }
    }
}

#[test]
pub fn test_hole() {
    let v = vec![1, 2, 3, 4, 5, 6, 8];
    assert!(find_hole(&v) == 7);
}

fn problem_5_b(filename: &str) -> usize {
    let lines = common::get_lines(filename).unwrap();
    let mut ids: Vec<_> = lines.iter().map(|line| {
        let (i, j) = calculate_pos(line);
        calculate_id(i, j)
    }).collect();
    
    ids.sort_unstable();

    find_hole(&ids)
}
{% endhighlight %}

I enjoyed this problem because I felt slightly clever in part 1
(although there is an easier way), and I recognized the binary search
trick in part 2 (despite it not being a huge time save).
