## 题目分析

* 为了解决该问题，可以采用的基本想法是什么？

  > 1. 用target不断去减候选数组中的数字。

* 基于上述基本想法，在如下案例中，解决该问题的计算过程是什么？对计算过程分析，可得出哪些结论？基于计算过程和分析结论，如果解决这种情况下的该问题？

  | 案例名称 | 过程分析结论                                                 | 计算规则                                                     |
  | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 示例1    | 节点值为target和数组值的差；路径权重是数组值；当节点值为0时，取得目标结果；当节点值<0时，取不到目标结果；不同路径的结果值可能相同 | 如果节点值为0；则该路径上的所有权重值构成一个结果；如果节点值<0；则该路径上的所有权重值不构成一个结果；如果结果中存在重复的值，则只取其中一个值 |
  |          |                                                              | 假如采用回溯的方法，那么如果节点值大于0，则继续向下遍历，找数字和为下一节点值的所有组合；如果节点值等于0，则将该节点的路径加入结果集并回溯上一节点，寻找其他路径；如果节点值小于0，则回溯至上一节点，寻找其他路径 |
  |          |                                                              | 基于上述规则，递归函数的功能是找到数字和为根节点值的所有组合 |
  | 示例2    | 同示例1                                                      | 同示例1                                                      |
  | 示例3    | 同示例1                                                      | 同示例1                                                      |
  | 示例4    | 同示例1                                                      | 同示例1                                                      |
  | 示例5    | 同示例1                                                      | 同示例1                                                      |

* 归纳总结上述计算规则，如何解决该问题？

  > 1. 采用回溯的方法。如果节点值大于0，则继续向下遍历，找数字和为下一节点值的所有组合；如果节点值等于0，则将该节点的路径加入结果集并回溯上一节点，继续寻找其他路径；如果节点值小于0，则回溯至上一节点，继续寻找其他路径
  > 2. 如果结果集中存在重复的值，则只取其中一个值

* 如何用伪代码实现上述计算规则？

  ```java
  //回溯算法基本框架代码
  public void dfs(TreeNode root, List<> res){
      if (){return ...};
      res.add();
      dfs(root更新值, res);
      res.remove();
  }
  
  //详细代码
  public void dfs(int target, List<List<int>> res){
      if (target < 0){ return; }
      if (target == 0){ 
          if (resList中不包含res){
              resList.add(new ArrayList<>(res));
          }
          return;
      }
      
      for ( int candidate : candidates){
          res.add(candidate);
          dfs(target-candidate, res);
          res.removeLast();
      }
  }
  
  public class Solution{
      public List<List<int>> combinationSum(int[] candidates, int target){
          List<List<int>> resList = new ArrayList<>();
          dfs(target, new ArrayList<>());
          return resList;
      }
  }
  ```

* 上述伪代码存在哪些问题？如何用java实现上述伪代码？

  > 1. resList中不包含res很难实现

* 基于上述分析，如何去除resList中的重复值？

  > 1. 原因分析：当循环搜索同一节点的所有下层节点时，假如第一个节点和第二个节点在下一轮搜索的起点相同，那么第二个节点中就会存在和第一个节点相同的值
  > 2. 解决方案：每一次循环搜索下层节点时，设置第i个节点和第i+1个节点下一轮搜索的起点

* 基于上述分析，如何改进上述代码？

  ```java
  class Solution {
      public List<List<Integer>> combinationSum(int[] candidates, int target) {
          List<List<Integer>> resList = new ArrayList<>();
          //设置首轮for循环搜索的起始节点
          dfs(0, target, candidates, new ArrayList<>(), resList);
          return resList;
      }
  
      private void dfs(int begin, Integer target, int[] candidates, List<Integer> mList, List<List<Integer>> resList){
          if (target < 0){
              return;
          }
  
          if (target == 0){
              resList.add(new ArrayList<>(mList));
              return;
          }
  
          //根据设置的for循环起始节点，执行for循环
          for (int i=begin; i<candidates.length; i++){
              mList.add(candidates[i]);
              //设置下一轮for循环搜索的起始节点
              dfs(i, target-candidates[i], candidates, mList, resList);
              mList.remove(mList.size()-1);
          }
      }
  }
  ```

  