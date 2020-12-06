---
layout: post
title: Advent of Code 2020 - Problem 3
categories: [ rust ]
---

Problem 3 of AOC 2020.

---

This one is fun! We're given a large rectangular grid of '.'s and '#'s
that wraps left-to-right, and have to calculate the number of '#'s we
hit when descending the grid along a certain slope.

At first glance I wanted to make a row and column counter and just
iterate both of them, but I realized we can derive the column from the
row (since it's just a line).

For part 1 I hardcoded the slope given, but I redid it in part 2:

{% highlight rust %}
fn count_on_slope(y: usize, x: usize, grid: &[Vec<char>]) -> usize {
    let n = grid.len();
    let m = grid[0].len();
    let points: Vec<(usize, usize)> = (0..n)
        .filter(|i| {i % y == 0})
        .map(|i| {(i, (i / y * x) % m)}).collect();
    points.iter().filter(|(i, j)| { grid[*i][*j] == '#' }).count()
}

fn problem_3_a(filename: &str) -> usize {
    let lines = common::get_lines(filename).unwrap();
    let grid: Vec<Vec<char>> = lines.iter().map(|line| {
        line.chars().collect()
    }).collect();
    count_on_slope(1, 3, &grid)
}
{% endhighlight %}

In short, we get the number of rows and columns then start iterating
on our row index. When our row index matches the row step (since in
part 2, we have a slope of 2 down, 1 right), we put the point `(i,
(i / y * x) % m)` in the output. This comes directly from the slope of
the line, and the modulo handles the wrapping at the left-right edge.

From there, iterate through the points and count those whose
coordinates have a '#' in the grid.

In part 2, we do the same thing for five slopes and take their product.

{% highlight rust %}

fn problem_3_b(filename: &str) -> usize {
    let lines = common::get_lines(filename).unwrap();
    let grid: Vec<Vec<char>> = lines.iter().map(|line| {
        line.chars().collect()
    }).collect();

    let slopes = vec![
        (1, 1),
        (1, 3),
        (1, 5),
        (1, 7),
        (2, 1)
    ];

    slopes.iter().map(|(y, x)| {
        let a = count_on_slope(*y, *x, &grid);
        println!("{} {} => {}", y, x, a);
        a
    }).product()
}
{% endhighlight %}

There are a bunch of iterator methods that seem interesting
(`filter_map`??) but so far I'm getting a lot of mileage out of the
basics.
