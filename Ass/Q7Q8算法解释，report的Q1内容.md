# 中文稿 + 代码
## Q7
---
为了实现允许用户通过ID或者价格或者品牌进行排序，我们通过自定义比较函数（例如`std::function<bool(const car_t&, const car_t&)>`）来指定排序的准则。使用归并排序来对车辆的ID、价格或品牌排序。选择归并排序是因为它具有稳定性和良好的时间复杂度（O(n log n)），尤其在处理较大数据集时性能表现稳定。
`merge_sort`函数通过递归的方式将传入的`std::vector<car_t>`类型的车辆列表不断分割为更小的子列表，直到每个子列表只包含一个元素。然后，它使用一个自定义的比较函数`cmp`将这些子列表合并成一个有序的列表。以品牌排序为例，比较函数可以定义为按品牌的字典顺序进行比较。当合并两个已排序的子列表时，`merge`函数使用这个比较函数依次比较左右子列表中的车辆品牌，将较小品牌的车辆放入合并后的列表中，直到所有车辆都被合并完毕。最终，得到按品牌排序之后的车辆列表。

```
void car_list==merge_sort(std==vector<car_t> &arr, int left, int right, std::function<bool(const car_t&, const car_t&)> cmp) const {
    if (left < right) {
        int mid = left + (right - left) / 2;
        merge_sort(arr, left, mid, cmp);
        merge_sort(arr, mid + 1, right, cmp);
        merge(arr, left, mid, right, cmp);
    }
}
```
```
void car_list==merge(std==vector<car_t> &arr, int left, int mid, int right, std::function<bool(const car_t&, const car_t&)> cmp) const {
    int n1 = mid - left + 1;
    int n2 = right - mid;
    std::vector<car_t> L(n1);
    std::vector<car_t> R(n2);
    for (int i = 0; i < n1; ++i){
        L[i] = arr[left + i];
	}
    for (int i = 0; i < n2; ++i){
	    R[i] = arr[mid + 1 + i];
    }
    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (cmp(L[i], R[j])) {
            arr[k++] = L[i++];
        } else {
            arr[k++] = R[j++];
        }
    }
    while (i < n1) {
        arr[k++] = L[i++];
    }
    while (j < n2) {
        arr[k++] = R[j++];
    }
}
```

## Q8
---
为了让用户能够查询查找通过品牌名通过排序后的最佳销量汽车品牌。我们使用了record_list::search_best_selling_car_by_brand函数通过结合冒泡排序和二分搜索实现这个功能。在`add_sale` 中方法记录某品牌的销量到`sale_count`中。首先使用冒泡排序算法对该向量按销量进行升序排序。冒泡排序通过逐步比较和交换相邻元素，确保每次迭代后最大的元素逐渐移至正确位置，直到整个`sale_count`有序。接着，代码使用二分搜索算法在已排序的向量中查找销量最大的品牌。二分搜索通过不断缩小搜索范围，有效地找到最大的销量值。最终，函数输出销量最高的品牌及其对应的销量。

```
void record_list::bubble_sort_brand_sale_pair_by_sale() {
    int n = sale_count.size();
    bool swapped;
    for (int i = 0; i < n - 1; ++i) {
        swapped = false;
        for (int j = 0; j < n - i - 1; ++j) {
            if (sale_count[j].first > sale_count[j + 1].first) {
                std::swap(sale_count[j], sale_count[j + 1]);
                swapped = true;
            }
        }
        if (!swapped) break;
    }
}
```

```
int& record_list==get_car_sold_by_brandname(std==string brandname) {
    int left = 0;
    int right = sale_count.size() - 1;
    int ans = -1;
    int mid;
    while (left <= right) {
        mid = left + (right - left) / 2;
        if (sale_count[mid].first == brandname) {
            ans = mid;
        } else if (sale_count[mid].first < brandname) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }
    if (ans == -1) {
        sale_count.push_back(std::make_pair(brandname, 0));
        ans = sale_count.size() - 1;
        bubble_sort_brand_sale_pair_by_sale();
    }
    return sale_count[ans].second;
}
```
# 英文稿
## Q7
---
To enable users to sort car record by ID, price, or brand, we specify the sorting criteria through a custom comparator function (`std==function<bool(const car_t&, const car_t&)>`). We use merge sort to sort vehicles by ID, price, or brand. Merge sort is chosen for its stability and good time complexity (O(n log n)), especially performing well with larger datasets. The merge_sort function recursively splits the input list of vehicles, a `std==vector<car_t>`, into smaller sublists until each sublist contains only one element. Then, it uses a custom comparator function `cmp` to merge these sublists into a sorted list. For example, when sorting by brand, the comparator function can be defined to compare brands in dictionary order. While merging two sorted sublists, the `merge` function uses this comparator to compare the brands of vehicles from the left and right sublists, placing the vehicle with the smaller brand into the merged list, until all vehicles are merged. This results in a list of vehicles sorted by brand.

## Q8
---
To enable users to query and find the best-selling car brand sorted by brand name, we use the `record_list::search_best_selling_car_by_brand` function, which combines bubble sort and binary search to achieve this functionality. In the `add_sale` method, we record the sales of a brand into `sale_count`. Initially, the vector is sorted in ascending order using the bubble sort algorithm. Bubble sort progressively compares and swaps adjacent elements, ensuring that the largest element gradually moves to its correct position with each iteration until the entire `sale_count` is ordered. Subsequently, the code uses the binary search algorithm to find the brand with the highest sales in the sorted vector. Binary search effectively locates the maximum sales value by continuously narrowing the search range. Finally, the function outputs the brand with the highest sales along with its corresponding sales volume.