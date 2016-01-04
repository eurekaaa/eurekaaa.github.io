---
layout: post
title:  "Heap Sort In Javascript"
date:   2016-1-4 11:00:00 +0900
categories: jekyll update
---

heap sort

{% highlight javascript %}

function HeapSort(list) {
    list.unshift(0);    // heap sort는 rootIdx를 1부터 시작하기 때문에 0번째에 의미없는 값을 넣어둔다.

    function adjust(rootIdx, n) {
        var childIdx, rootKey;
        rootKey = list[rootIdx];
        childIdx = 2 * rootIdx;
        while(childIdx <= n) {
            if ((childIdx < n) && (list[childIdx] < list[childIdx + 1])) {
                childIdx++;
            }
            if (rootKey > list[childIdx]) {
                break;
            } else {
                list[parseInt(childIdx / 2)] = list[childIdx];
                childIdx *= 2;
            }
        }
        list[parseInt(childIdx/2)] = rootKey;
    }

    function swap(n, m) {
        var temp = list[n];
        list[n] = list[m];
        list[m] = temp;
    }

    function doSort(n) {
        var i, j, temp;

        for(i=parseInt(n/2); i>0; i--) {
            adjust(i, n);
        }

        for(i=n-1; i>0; i--) {
            swap(1, i+1);
            adjust(1, i);
        }
        list.shift();   // 0번째에 의미없이 넣어둔 값을 제거한다.
    }

    this.sort = function() {
        doSort(list.length-1);
    }

    this.print = function() {
        document.write('<br>result : ' + list);
    }
}

var arr = [2, 5, 1, 10, 6, 8, 9, 4, 3, 7];
var h = new HeapSort(arr);
h.sort();
h.print();

{% endhighlight %}