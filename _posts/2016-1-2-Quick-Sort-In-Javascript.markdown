---
layout: post
title:  "Quick Sort In Javascript"
date:   2016-1-2 13:01:00 +0900
categories: jekyll update
---

quick sort

{% highlight javascript %}

function swap(list, i, j) {
    var temp = list[i];
    list[i] = list[j];
    list[j] = temp;
}

function quickSort(list, left, right) {
    var pivot, i, j;
    if(left < right) {
        i = left;
        j = right + 1;
        pivot = list[left];

        do {
            do
                i++;
            while (list[i] < pivot);
            do
                j--;
            while (list[j] > pivot);
            if(i<j) swap(list, i, j);
        } while(i < j);

        swap(list, left, j);
        quickSort(list, left, j-1);
        quickSort(list, j+1, right);
    }
}

var arr = [10, 5, 3, 88, 31, 28, 97, 50, 23, 67];
console.log(arr.toString());
quickSort(arr, 0, arr.length-1);
console.log(arr.toString());

{% endhighlight %}