---
layout: default
---

<!-- mathjax config similar to math.stackexchange -->
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.5/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    TeX: { equationNumbers: { autoNumber: "AMS" }},
    tex2jax: {
      inlineMath: [ ['$','$'] ],
      processEscapes: true
    },
    "HTML-CSS": { matchFontHeight: false },
    displayAlign: "left",
    displayIndent: "2em"
  });
</script>

<script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.4.1/jquery.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/jquery-balloon-js@1.1.2/jquery.balloon.min.js" integrity="sha256-ZEYs9VrgAeNuPvs15E39OsyOJaIkXEEt10fzxJ20+2I=" crossorigin="anonymous"></script>
<script type="text/javascript" src="../../assets/js/copy-button.js"></script>
<link rel="stylesheet" href="../../assets/css/copy-button.css" />


# :heavy_check_mark: a Segment Tree (generalized with monoids)
* category: examples


[Back to top page](../../index.html)

* set $a_i$ as b in $O(\log n)$
* compute $a_l \cdot a _ {l + 1} \cdot ... \cdot a _ {r - 1}$ in $O(\log n)$


## Verified
* :heavy_check_mark: [examples/segment_tree.point_set_range_composite.test.cpp](../../verify/examples/segment_tree.point_set_range_composite.test.cpp.html)
* :heavy_check_mark: [examples/segment_tree.range_minimum_query.test.cpp](../../verify/examples/segment_tree.range_minimum_query.test.cpp.html)
* :heavy_check_mark: [examples/segment_tree.range_sum_query.test.cpp](../../verify/examples/segment_tree.range_sum_query.test.cpp.html)


## Code
{% raw %}
```cpp
#pragma once
#include <cassert>
#include <vector>

/**
 * @brief a Segment Tree (generalized with monoids) 
 * @tparam Monoid is a monoid; commutativity is not required
 */
template <class Monoid>
struct segment_tree {
    typedef typename Monoid::value_type value_type;
    const Monoid mon;
    int n;
    std::vector<value_type> a;

    segment_tree() = default;
    segment_tree(int n_, const Monoid & mon_ = Monoid()) : mon(mon_) {
        n = 1; while (n < n_) n *= 2;
        a.resize(2 * n - 1, mon.unit());
    }

    /**
     * @brief set $a_i$ as b in $O(\log n)$
     * @arg i is 0-based
     */
    void point_set(int i, value_type b) {
        assert (0 <= i and i < n);
        a[i + n - 1] = b;
        for (i = (i + n) / 2; i > 0; i /= 2) {  // 1-based
            a[i - 1] = mon.mult(a[2 * i - 1], a[2 * i]);
        }
    }

    /**
     * @brief compute $a_l \cdot a _ {l + 1} \cdot ... \cdot a _ {r - 1}$ in $O(\log n)$
     * @arg l, r are 0-based
     */
    value_type range_concat(int l, int r) {
        assert (0 <= l and l <= r and r <= n);
        value_type lacc = mon.unit(), racc = mon.unit();
        for (l += n, r += n; l < r; l /= 2, r /= 2) {  // 1-based loop, 2x faster than recursion
            if (l % 2 == 1) lacc = mon.mult(lacc, a[(l ++) - 1]);
            if (r % 2 == 1) racc = mon.mult(a[(-- r) - 1], racc);
        }
        return mon.mult(lacc, racc);
    }
};

```
{% endraw %}

[Back to top page](../../index.html)
