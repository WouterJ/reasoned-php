# reasoned-php

A [miniKanren](http://minikanren.org/) in PHP, based on the microKanren paper.

## Examples

### eq

    var_dump(run_star(function ($q) {
        return eq($q, 'corn');
    }));

    // => ['corn']

### conde

    var_dump(run_star(function ($q) {
        return conde([
            [eq($q, 'tea')],
            [eq($q, 'cup')],
        ]);
    }));

    // => ['tea', 'cup']

### firsto

    var_dump(run_star(function ($q) {
        return firsto(['a', 'c', 'o', 'r', 'n'], $q);
    }));

    // => ['a']

### resto

    var_dump(run_star(function ($q) {
        return resto(['a', 'c', 'o', 'r', 'n'], $q);
    }));

    // => [['c', 'o', 'r', 'n']]

### all

    var_dump(run_star(function ($q) {
        return all([
            firsto(['a', 'l'], $q),
            firsto(['a', 'x'], $q),
            firsto(['a', 'z'], $q),
        ]);
    }));

    // => ['a']

### fresh

    var_dump(run_star(function ($q) {
        return fresh(function ($x) use ($q) {
            return all([
                eq(['d', 'a', $x, 'c'], $q),
                conso($x, ['a', $x, 'c'], $q),
            ]);
        });
    }));

    // => ['d', 'a', 'd', 'c']

### membero

    function membero($x, $l) {
        return conde([
            [fresh(function ($d) use ($x, $l) {
                return conso($x, $d, $l);
            })],
            [fresh(function ($a, $d) use ($x, $l) {
                return all([
                    conso($a, $d, $l),
                    membero($x, $d),
                ]);
            })],
        ]);
    }

    var_dump(run_star(function ($q) {
        return all([
            membero($q, [1, 2, 3]),
            membero($q, [2, 3, 4]),
        ]);
    }));

    // => [2, 3]

### run

    var_dump(run(3, function ($q) {
        return membero('tofu', $q);
    }));

    // => [['tofu', '.', '_.0']
    //     ['_.0', 'tofu', '.', '_.1']
    //     ['_.0', '_.1', 'tofu', '.', '_.2']]

### appendo

    var_dump(run_star(function ($q) {
        return appendo([1, 2, 3], [4, 5, 6], $q);
    }));

    // => [[1, 2, 3, 4, 5, 6]]

## See also

* [The Reasoned Schemer](http://mitpress.mit.edu/books/reasoned-schemer)
* [miniKanren](http://minikanren.org/)
* [microKanren](http://webyrd.net/scheme-2013/papers/HemannMuKanren2013.pdf)
