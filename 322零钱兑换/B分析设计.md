### 基本想法

* 为了解决该问题，可采用的基本想法是什么？

  > 1. 按从大到小的顺序排列可选面额，每次用剩余总金额减去可选面额

* 基于上述基本想法，在如下案例中，解决该问题的计算过程是什么？对计算过程分析，可得出哪些结论？基于计算过程和分析结论，如何解决这种情况下的该问题？

  | 案例名称 | 过程分析结论                                                 | 计算规则                                                     |
  | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 示例1    | 当节点值为0时，该节点所在的层数就是最少的硬币数；当节点值<0时，匹配失败并返回上一节点，继续尝试其他金额；当所有可选结果都匹配失败，返回上一节点继续尝试其他可能。根节点对应的面额数为0，第i层节点对应的面额数为i | 假如每层节点记录当前节点层数，如果当前节点的值为0，则当前节点层数就是最终结果，则直接返回结果；如果当前节点值<0，则返回上一节点，继续尝试其他可能；当当前节点的所有可能都尝试失败，则返回上一节点继续尝试其他情况 |
  | 示例2    | 当不存在匹配结果时，返回结果为-1                             | 如果无法凑出当前总金额，则返回值为-1                         |
  | 示例3    | 当总金额为0时，返回结果为0                                   | 如果当前总金额为0，则返回值为0                               |
  | 示例4    | 同上                                                         | 同上                                                         |
  | 示例5    | 同上                                                         | 同上                                                         |

* 归纳总结上述计算规则，如何解决该问题？

  > 1. 判断当前总金额是否为0，如果为0，则直接返回0，否则，递归遍历当前总金额
  > 2. 记录当前节点层数，如果当前总金额为0，则返回当前节点层数；如果当前总金额<0，则返回上一节点；如果当前总金额>0，则尝试选择下一个面额；如果该节点下的所有尝试都失败，则返回上一节点尝试其他情况
  > 3. 如果所有尝试都失败，则返回值为-1

* 如何用伪代码实现上述计算规则？

  ```java
  class Solution {
      public int coinChange(int[] coins, int amount) {
          if (amount==0) {
              return 0;
          }
          int[] sortCoins = 从大到小排列coins数字中的面额;
          int res = dfs(0, sortCoins, amount);
          return res;
      }
      
      private int dfs(int depth, int[] sortCoins, int amount){
          if (amount == 0) {
              return depth;
          }
          
          if (amount < 0){
              return -1;
          }
          
          for (int i=0; i < sortCoins.length; i++){
              int res = dfs(depth+1, sortCoins[i], amount-sortCoins[i]);
              if (res>-1) {
                  return res;
              }
          }
          return -1
      }
  }
  ```


* 如何java实现上述伪代码？

  ```java
  class Solution {
      public int coinChange(int[] coins, int amount) {
          Integer[] what = Arrays.stream( coins ).boxed().toArray( Integer[]::new );
          Arrays.sort(what,Collections.reverseOrder());
          int res = dfs(0, 0, what, amount);
          return res;
      }
  
      private int dfs(int begin, int depth, Integer[] sortCoins, int amount){
          if (amount==0){
              return depth;
          }
  
          if (amount > 0){
              for(int i=0; i < sortCoins.length; i++){
                  int res = dfs(i, depth+1, sortCoins, amount - sortCoins[i]);
                  if (res > -1){
                      return res;
                  }
              }
          }
  
          return -1;
      }
  }
  ```

* 上述方案存在哪些问题？

  > 1. 上述方案中，每次都选面额最大的钱，并不能保证选择的钱数是最少的，即首次得到的解并不一定是最优解
  > 2. 因此，该题目无法用贪心求解

### 想法调整

* 基于上述分析，为了解决上述问题，调整后的基本想法是什么？

  > 1. 枚举所有可能的情况，找到所有可以凑出总金额的解，并在这些解中找到最优解

* 基于上述基本想法，在如下案例中，解决该问题的计算过程是什么？对计算过程分析，可得出哪些结论？基于计算过程和分析结论，如何解决这种情况下的该问题？

  | 案例名称 | 过程分析结论                                                 | 计算规则                                                     |
  | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 示例1    | 当节点值为0时，该节点所在的层数就是一个可能解；当节点值<0时，匹配失败并返回上一节点，继续尝试其他金额；当所有可选结果都匹配失败，返回上一节点继续尝试其他可能。根节点对应的面额数为0，第i层节点对应的面额数为i | 假如每层节点记录当前节点层数，如果当前节点的值为0，则当前节点层数就是一个可能解，则比较当前最优解和该可能解，并返回二者的最小值；如果当前节点值<0，则返回上一节点，继续尝试其他可能；当当前节点的所有可能都尝试失败，则返回上一节点继续尝试其他情况；如果返回到根节点，则遍历结束，返回结果就是最优解 |
  | 示例2    | 当不存在匹配结果时，返回结果为-1                             | 如果无法凑出当前总金额，则返回值为-1                         |
  | 示例3    | 当总金额为0时，返回结果为0                                   | 如果当前总金额为0，则返回值为0                               |
  | 示例4    | 同上                                                         | 同上                                                         |
  | 示例5    | 同上                                                         | 同上                                                         |

* 归纳总结上述计算规则，如何解决该问题？

  > 1. 判断当前总金额是否为0，如果为0，则直接返回0，否则，递归遍历当前总金额
  > 2. 记录当前节点层数，如果当前总金额为0，则比较当前节点层数和最优解，并返回二者的最小值；如果当前总金额<0，则返回上一节点；如果当前总金额>0，则尝试选择下一个面额；如果该节点下的所有尝试都失败，则返回上一节点尝试其他情况
  > 3. 如果所有尝试都完成并且没有找到解，则返回值为-1；如果所有尝试结束都完成并且找到解，则返回值为最优解

* 如何用伪代码实现上述计算规则？

  ```java
  class Solution {
      int minist = 最大值;
      public int coinChange(int[] coins, int amount) {
          if (amount==0) {
              return 0;
          }
          int res = dfs(0, minist, sortCoins, amount);
          if (minist==最大值){
              return -1
          }
          return minist;
      }
      
      private int dfs(int depth, int minist, int[] sortCoins, int amount){
          if (amount < 0){
              return minist;
          }
          
          if (amount == 0) {
              minist = min(depth, minist);
              return minist;
          }
          
          for (int i=0; i < sortCoins.length; i++){
              int minist = dfs(depth+1, minist, sortCoins[i], amount-sortCoins[i]);
          }
          return minist;
      }
  }
  ```

* 如何用java实现上述伪代码？

  ```java
  class Solution {
      int minist = Integer.MAX_VALUE;
      public int coinChange(int[] coins, int amount) {
          dfs(0, minist, coins, amount);
          if (minist==Integer.MAX_VALUE){
              return -1;
          }
          return minist;
      }
  
      private void dfs(int depth, int minist, int[] coins, int amount){
          if (amount==0){
              minist = Math.min(depth, minist);
          }
  
          if (amount > 0){
              for(int i=0; i < coins.length; i++){
                  dfs(depth+1, minist, coins, amount - coins[i]);
              }
          }
  
          return;
      }
  }
  ```

* 上述代码有什么问题？如何修改上述代码？

  ```java
  //    private void dfs(int depth, int minist, int[] coins, int amount)--假如minist作为函数入参，则每个函数中minist的值都会改变，则无法统计全局信息
  // 解决方案：将minist值从函数入参中删掉
  class Solution {
      int minist = Integer.MAX_VALUE;
      public int coinChange(int[] coins, int amount) {
          dfs(0, coins, amount);
          if (minist==Integer.MAX_VALUE){
              return -1;
          }
          return minist;
      }
  
      private void dfs(int depth, int[] coins, int amount){
          if (amount==0){
              minist = Math.min(depth, minist);
          }
  
          if (amount > 0){
              for(int i=0; i < coins.length; i++){
                  dfs(depth+1, coins, amount - coins[i]);
              }
          }
  
          return;
      }
  }
  ```

* 上述方案存在哪些问题？

  > 1. 时间复杂度太高，在某些用例上超出了时间限制

### 想法调整2

* 基于上述分析，为了解决该问题，调整后的基本想法是什么？

  > 1. 设置一个记忆数组，记录相应总金额需要的最少面额数

* 基于上述基本想法，在如下案例中，解决该问题的计算过程是什么？对计算过程分析，可得出哪些结论？基于计算过程和分析结论，如何解决这种情况下的该问题？

  | 案例名称 | 过程分析结论                                                 | 计算规则                                                     |
  | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 示例1    | 当总金额为0时，最少硬币数为0；当总金额>0时，最少硬币数为下层节点的最少硬币数+1；当总金额<0时，不在记忆数组的索引范围内，直接尝试其他可能情况 | 假如采用回溯+记忆数组的方式，如果节点值为0，则更新记忆数组0位置为0；如果节点值>0，则获取所有下层节点的返回值；如果下层节点的返回值>0并且比当前的结果值小，则更新记忆数组(节点值-1)位置为下层节点的返回值+1；如果遍历完所有下层节点，则返回所有下层节点的返回值的最小值；如果当前节点值<0，则返回值为-1；如果下层节点的返回值<0，则不更新记忆数组 |
  | 示例2    | 当不存在匹配结果时，返回结果为-1                             | 如果记忆数组的对应值未更新，则返回值为-1                     |
  | 示例3    | 当总金额为0时，返回结果为0                                   | 记忆数组索引0处的值为0                                       |
  | 示例4    | 同上                                                         | 同上                                                         |
  | 示例5    | 同上                                                         | 同上                                                         |

* 归纳总结上述计算规则，如何解决该问题？

  > 1. 设置一个记忆数组，并初始化数组值为最大；这只索引0位置的值为0
  > 2. 如果节点值=0，则直接返回记忆数组的值；如果节点值<0，则返回-1；如果节点值>0，则for循环遍历下层节点的值，并接收下层节点的返回值res；判断res是否>0并且<记忆数组的值，若是，则更新记忆数组值为res；否则，继续尝试下一个下层节点；如果所有下层节点都尝试完成并且记忆数组对应值未更新，则返回-1；如果所有下层节点都尝试完成并且记忆数组对应值已更新，则返回记忆数组的对应值
  > 3. 接收处理函数的返回值，直接返回

* 如何用伪代码实现上述计算规则？

  ```java
  class Solution {
      int[] minist;
      public int coinChange(int[] coins, int amount) {
          for (int i=0; i<amount-1; i++){
              minist[i] = Integer.MAX_VALUE;
          }
          minist[0] = 0;
          int res = dfs(coins, amount);
          return res;
      }
      
      private void dfs(int[] coins, int amount){
          if (amount==0){
              return 0;
          }
          
          if (amount < 0){
              return -1;
          }
          
          for (int i=0; i < coins.length; i++){
              int res = dfs(coins, amount-coins[i]);
              if (res > 0 && res < minist[amount]){
                  minist[amount] = res;
              }
          }
          
          if (minist[amount]==Integer.MAX_VALUE){
              return -1;
          }else{
              return minist[amount];
          }
      }
  }
  ```

* 如何用java实现上述伪代码？

  ```java
  class Solution {
      int[] minist;
      public int coinChange(int[] coins, int amount) {
          minist = new int[amount+1];
          for (int i=0; i<amount+1; i++){
              minist[i] = Integer.MAX_VALUE;
          }
          minist[0] = 0;
          int res = dfs(coins, amount);
          return res;
      }
      
      private int dfs(int[] coins, int amount){
          if (amount==0){
              return minist[0];
          }
  
          if (amount < 0){
              return -1;
          }
  
          if (minist[amount] != Integer.MAX_VALUE){
              return minist[amount];
          }
          
          for (int i=0; i < coins.length; i++){
              int res = dfs(coins, amount-coins[i]);
              if (res >= 0 && res < minist[amount]){
                  minist[amount] = res+1;
              }
          }
          
          if (minist[amount]==Integer.MAX_VALUE){
              return -1;
          }else{
              return minist[amount];
          }
      }
  }
  ```

* 为什么上述代码仍然会超出时间限制？如何改进上述代码？

  ```java
  // 超出时间限制的原因：1.初始化minist时，专门的for循环太耗时
  //                   2.最后判断记忆数组是否更新时，采用if...else...太耗时
  class Solution {
      int[] minist;
      public int coinChange(int[] coins, int amount) {
          //省去for循环初始化的时间，在递归遍历树时顺带初始化
          minist = new int[amount+1];
          int res = dfs(coins, amount);
          return res;
      }
      
      private int dfs(int[] coins, int amount){
          if (amount==0){
              return minist[0];
          }
  
          if (amount < 0){
              return -1;
          }
  
          //java中，初始化数组的每个值为0
          if (minist[amount] != 0){
              return minist[amount];
          }
          
          int min = Integer.MAX_VALUE;
          for (int i=0; i < coins.length; i++){
              int res = dfs(coins, amount-coins[i]);
              if (res >= 0 && res < min){
                  min = res+1;
              }
          }
          //使用单行语句替代if...else...，提高效率
          minist[amount] = (min == Integer.MAX_VALUE ? -1 : min);
          return minist[amount];
      }
  }
  ```





















