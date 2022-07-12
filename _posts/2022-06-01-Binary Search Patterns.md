# Binary Search: Patterns to understand algorithm design
## Patterns

Interviewing bar is getting higher and higher for software engineers. Binary search is a topic
that is elegant and has a lot of room for customization. Just some notes as I learn a bit of Binary search

Template:

1. Initialize the search space (start and end)
2. Find middle of the search space
3. Discard/select one half based on the specific condition, choice of loop break condition
is important. 
4. Update the search space (this also depends on the condition and choice of start and end)
5. Break the loop once the search space is narrowed down to a single element

## Template code

If we choose left and right based on the size of the search space

`class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left,right = 0,len(nums)
        
        while left < right:
            mid = left + (right - left)//2
            
            if nums[mid] >= target:
                right = mid
            else:
                left = mid+1
        
        if nums[left] == target:
            return left
        else:
            return -1`

Or if we choose left and right as first and last index

`class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left,right = 0,len(nums)-1
        
        while left <= right:
            mid = left + (right - left)//2
            
            if nums[mid] >= target:
                right = mid-1
            else:
                left = mid+1
        
        if left < len(nums) and nums[left] == target:
            return left
        else:
            return -1`


## Standard library support for Binary Search (Always ask the interviewer if the question is primarily about Binary search)

### Python Bisect Module
1. Python bisect module has four methods that can help us skip the implemnetation of Binary search and use it out of the box.
2. bisect.bisect_left(a, x, lo=0, hi=len(a), *, key=None), locate left-most insertion point for x in a, lo and hi can be used to indicate the search subset, ignore the key for now
3. bisect.bisect_right(a, x, lo=0, hi=len(a), *, key=None), locate right-most insertion point for x in a, lo and hi can be used to indicate the search subset, ignore the key for now
4. bisect.insort_left(a, x, lo=0, hi=len(a), *, key=None), Insert after finding the correct leftmost place for x in a
5. bisect.insort_right(a, x, lo=0, hi=len(a), *, key=None), Insert after finding the correct rightmost place for x in a
6. bisect.insort(a, x, lo=0, hi=len(a), *, key=None), essentially same as insort_right

### Java Arrays.binarysearch(array,key)

##Follow-up questions

1. How would you search an unbounded array?
2. Why only binary search, why not a ternary search, i.e divide the array into three parts?