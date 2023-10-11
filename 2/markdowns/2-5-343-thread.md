
# Post \#64525220 [Link](https://stackoverflow.com/questions/64525220/)

## Select two equally-sized disjoint subarrays, A and B, that maximise the sum (A_1*B_k + A_2*B_(k-1) ... + A_k*B_1), k = |A| = |B|

**Vote**: 3 (575/702) **Views**: 8317 (452/702) 

**Internal ID** \#2-5-343

Created at 2020-10-25 15:05:59

Tags: `java` `algorithm`

----------

#### Metadata:

Area: `Back-end`

Language: `java` (full parsed tag list: `java`)

----------

**Notepad**


----------

A food fest is organised at the JLN stadium. The stalls from different states and cities have been set up. To make the fest more interesting, multiple games have been arranged which can be played by the people to win the food vouchers.One such game to win the food vouchers is described below:
There are N number of boxes arranged in a single queue. Each box has an integer I written on it. From the given queue, the participant has to select two contiguous subsequences A and B of the same size. The selected subsequences should be such that the summation of the product of the boxes should be maximum. The product is not calculated normally though. To make the game interesting, the first box of subsequence A is to be multiplied by the last box of subsequence B. The second box of subsequence A is to be multiplied by the second last box of subsequence B and so on. All the products thus obtained are then added together.
If the participant is able to find the correct such maximum summation, he/she will win the game and will be awarded the food voucher of the same value.
Note: The subsequences A and B should be disjoint.
Example:
Number of boxes, N = 8
The order of the boxes is provided below:

Subsequence A

Subsequence B

The product of the subsequences will be calculated as below:
P1 = 9 * 8 = 72
P2 = 2 * 7 = 14
P3 = 3 * 6 = 18
Summation, S = P1 + P2 + P3 = 72 + 14 + 18 = 104
This is the maximum summation possible as per the requirement for the given N boxes.
Tamanna is also in the fest and wants to play this game. She needs help in winning the game and is asking for your help. Can you help her in winning the food vouchers?
Input Format
The first line of input consists of the number of boxes, N.
The second line of input consists of N space-separated integers.
Constraints
1< N <=3000
-10^6 <= I <=10^6
Output Format
Print the maximum summation of the product of the boxes in a separate line.
Sample TestCase 1
input
```
8
1 9 2 3 0 6 7 8
```

output
```
104
```

my code is this it is passing only one test can anyone tell me what is wrong and i don't have other test cases since they r hidden
```
import java.util.Scanner;
import java.util.*;

public class Main {
    
    static class pair {
        int first, second;
        
        public pair(int first, int second) {
            this.first = first;
            this.second = second;
        }
    }
    
    static int getSubarraySum(int sum[], int i, int j) {
        if (i == 0)
            return sum[j];
        else
            return (sum[j] - sum[i - 1]);
    }
    
    static int maximumSumTwoNonOverlappingSubarray(int arr[], int N,
            int K) {
        int l = 0, m = 0;
        int a1[] = new int[N / 2];
        int a2[] = new int[N / 2];
        int prod = 0;
        int[] sum = new int[N];
        sum[0] = arr[0];
        
        for (int i = 1; i < N; i++)
            sum[i] = sum[i - 1] + arr[i];
        
        pair resIndex = new pair(N - 2 * K, N - K);
        
        int maxSum2Subarray =
                getSubarraySum(sum, N - 2 * K, N - K - 1)
                        + getSubarraySum(sum, N - K, N - 1);
        
        pair secondSubarrayMax =
                new pair(N - K, getSubarraySum(sum, N - K, N - 1));
        
        for (int i = N - 2 * K - 1; i >= 0; i--) {
            int cur = getSubarraySum(sum, i + K, i + 2 * K - 1);
            if (cur >= secondSubarrayMax.second)
                secondSubarrayMax = new pair(i + K, cur);
            cur = getSubarraySum(sum, i, i + K - 1)
                    + secondSubarrayMax.second;
            if (cur >= maxSum2Subarray) {
                maxSum2Subarray = cur;
                resIndex = new pair(i, secondSubarrayMax.first);
            }
        }
        
        for (int i = resIndex.first; i < resIndex.first + K; i++) {
            a1[l] = arr[i];
            l++;
        }
        
        for (int i = resIndex.second; i < resIndex.second + K; i++) {
            a2[m] = arr[i];
            m++;
        }
        
        for (int i = 0; i < m; i++) {
            if (a1[i] != 0 || a2[i] != 0) {
                prod = prod + a1[i] * a2[m - (i + 1)];
            }
        }
        return prod;
    }
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int a = sc.nextInt();
        int k = 0;
        int arr[] = new int[a];
        
        for (int i = 0; i < a; i++) {
            arr[i] = sc.nextInt();
        }
        
        int l = arr.length;
        int ar[] = new int[a / 2];
        
        for (int i = 1; i <= a / 2; i++) {
            ar[k] = maximumSumTwoNonOverlappingSubarray(arr, l, i);
            k++;
        }
        
        Arrays.sort(ar);
        System.out.println(ar[k - 1]);
    }
}
```



----------
        
## Answer \#0

**Accepted** Vote: 1

Created at 2020-11-05 07:58:03

------------

```
#include <iostream>

#include <cassert>

using namespace std;

template<class T> inline void umax(T &a,T b){if(a<b) a = b ; }

template<class T> inline void umin(T &a,T b){if(a>b) a = b ; }

template<class T> inline T abs(T a){return a>0 ? a : -a;}

template<class T> inline T gcd(T a,T b){return __gcd(a, b);}

template<class T> inline T lcm(T a,T b){return a/gcd(a,b)*b;}

typedef long long ll;

typedef pair<int, int> ii;

const int inf = 1e9 + 143;

const ll longinf = 1e18 + 143;

inline int read()

{

int x;scanf(" %d",&x);

return x;

}

const int N = 20001;

int n;

int a[N];

void read_inp()

{

   n = read();

   assert(1 <= n && n <= 20000);

   for(int i = 1; i <= n; i++)

{

       a[i] = read();

       assert(abs(a[i]) <= int(1e6));

   }

}

int main()

{

#ifdef KAZAR

   freopen("f.input","r",stdin);

   freopen("f.output","w",stdout);

   freopen("error","w",stderr);

#endif

   read_inp();

   ll ans = -longinf;

   for(int i = 1; i <= n; i++)

{

       {

           int l = i - 1, r = i;

           ll best = 0ll, cur = 0ll;

           while(l >= 1 && r <= n)

  {

               ll val = (ll)a[l] * a[r];

               cur += val;

               umin(best, cur);

               umax(ans, cur - best);

               --l;

               ++r;

           }

       }

 {

           int l = i - 1, r = i + 1;

           ll best = 0ll, cur = 0ll;

           while(l >= 1 && r <= n)

  {

               ll val = (ll)a[l] * a[r];

               cur += val;

               umin(best, cur);

               umax(ans, cur - best);

               --l;

               ++r;

           }

       }

   }

   printf("%lld\n",ans);

   return 0;

}
```



------------
    
    
## Answer \#1

 Vote: 3

Created at 2020-10-26 06:25:38

------------

Here's an `O(n^2)` time, `O(1)` space solution.
Lets write all `O(n^2)` multiples in a matrix. For example:
```
Input {1, 2, 3, -4, 5, 6}

    1   2   3  -4   5   6
 1  x   2   3  -4   5   6
 2      x   6  -8  10  12
 3          x -12  15  18
-4              x -20 -24
 5                  x  30
 6                      x
```

Now pick any indexes `(i, j), i ≠ j`, say `(0, 5)`.
```
j
      1   2   3  -4   5   6
i  1  x   2   3  -4   5   6
   2      x   6  -8  10  12
   3          x -12  15  18
  -4              x -20 -24
   5                  x  30
   6                      x
```

Now imagine we wanted to find the best subarray where `i` was first, then second, then third, etc. of a valid selection. In each iteration, we would increment `i` and decrement `j`, such that we move on the diagonal: `6, 10, -12`, each time adding the multiple to extend our selection.
We can do this on each of the diagonals to get the best selection starting on `(i, j)`, where `i` is first, then second, then third, etc.
Now imagine we ran [Kadane's algorithm](https://en.wikipedia.org/wiki/Maximum_subarray_problem#Kadane%27s_algorithm) on each of the diagonals from northeast to southwest (up to where the `x`s are where `i = j`). Complexity `O(n^2)` time. (There's Python code in one of the [revisions](https://stackoverflow.com/posts/64532360/revisions).)


------------
    
    
## Answer \#2

 Vote: 2

Created at 2020-10-29 13:13:01

------------

Here is the code
```
n=int(input())
l=[]
res=0
l=list(map(int,input().split()))
re=[]
while(True):
    if(len(l)==2):
        pass
        break
    else:
        n1=l[1]
        n2=l[-1]
        re.append(n1*n2)
        l.remove(n1)
        l.remove(n2)
for i in re:
    res=res+i
print(res)
```



------------
    
    
## Answer \#3

 Vote: -1

Created at 2020-10-28 08:38:54

------------

Here is the code
```
int main(){

int n;
cin>>n;
int arr[n];
for(int i=0;i<n;i++) 
    cin>>arr[i];
int dp[n][n];
for(int i=0;i<n;i++)
{
    for(int j=0;j<n;j++)
    {

        if(j==i) 
            dp[i][j]=0;
        else if(j<i)
            dp[i][j]=0;
        else 
            dp[i][j]=arr[i]*arr[j];
    }

}
cout<<endl;
for(int i=0;i<n;i++)
   {
       for(int j=0;j<n;j++)
            cout<<dp[i][j]<<"   ";
        cout<<endl;
    }

    cout<<endl;
//find max sum diagonal
long long int global_sum=0;

//get sum of diagonal increasing i
for(int i=0;i<n;i++)
{
    long long int curr_sum=0;
    int j=i;
    int k=n-1;
    while(k>=0 && j<n){
        curr_sum+=dp[j][k];
        k--;
        j++;
    }
    if(curr_sum>global_sum) global_sum=curr_sum;

}
//get sum with decreasing i
for(int i=n-1;i>=0;i--){
    long long int curr_sum=0;
    int j=i;
    int k=0;
    while(k<n && j>=0){
        curr_sum+=dp[j][k];
        j--;
        k++;
    }
    if(curr_sum>global_sum) global_sum=curr_sum;
}
cout<<global_sum;}
```

This code passes the testcase you gave and other testcases i tried myself. Its O(n^2) complexity.


------------
    
    