### 基本想法

* 为了解决该问题，可采用的基本想法是什么？

  > 1. 拿单词中的字母与表格中的字母一个一个匹配

* 基于上述基本想法，在如下案例中，解决该问题的计算过程是什么？对计算过程分析，可得出哪些结论？基于计算过程和分析结论，如何解决这种情况下的该问题？

  | 案例名称 | 过程分析结论                                                 | 计算规则                                                     |
  | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
  | 示例1    | 开始时，需要通过遍历确定首字母匹配成功的位置；首字母匹配成功后，需要循环判断上下左右四个方向的字母是否与下一字母匹配 | 循环遍历网格内的字母，如果首字母匹配成功，则开始匹配下一字母；假如上一字母匹配成功，循环判断上下左右字母是否与下一字母匹配，如果不匹配，则顺时针匹配下一位置；如果匹配，则以匹配位置为起点，继续匹配下以字母；如果四个方向字母都不匹配，则匹配失败，则回溯至上一节点；对于匹配结果，如果匹配字母长度为0，则匹配成功，则最终结果是成功；如果所有位置的字母都匹配失败，则最终结果是不存在 |
  | 示例2    | 循环判断，上下左右方向的字母中可能存在已匹配的字母           | 循环判断上下左右字母是否与下一字母匹配，在上下左右字母中，假如某位置的字母已匹配，则跳过该字母 |
  | 示例3    | 同上                                                         | 同上                                                         |
  |          |                                                              | 假如采用递归的思想，那么判断当前匹配字母的周围是否存在剩余子串的匹配字符串，如果存在，则匹配成功，否则匹配失败 |
  
* 归纳总结上述计算规则，如何解决该问题？

  > 1. 循环遍历网格内的字母，如果首字母匹配成功，则判断当前匹配字母的周围是否存在剩余字串的匹配字符串
  > 1. 在判断当前匹配字母的周围是否存在剩余子串的匹配字符串的过程中，循环判断上下左右字母是否与下一字母匹配，如果不匹配，则顺时针匹配下一位置；如果该位置已匹配，则跳过该位置；如果匹配，则以匹配位置为起点，继续判断该位置周围是否存在剩余子串的匹配字符串；如果匹配字母长度为0，则匹配成功；如果四个方向字母都不匹配，则匹配失败，则回溯至上一节点
  > 1. 如果匹配过程中有一个结果匹配成功，则最终结果为存在；如果所有位置的字母都匹配失败，则最终结果是不存在

* 如何用代码实现上述计算规则？

  ```java
  public class Solution{
      public boolean exist(char[][] board, String word){
          List<String> memoryList = new ArrayList<>();
          for (int i = 0; i < board.size(); i++){
              for (int j = 0; j < board[0].size(); j++){
                  if (board[i][j].equals(word[0]) && existSub(i, j, board, memoryList.add(Integer.toString(i) + "," + Integer.toString(j)), word.subString(1))){
                      return true
                  }
              }
          }
          return false;
      }
      
      //java中return后面不能返回a || b || c || d
      private boolean existSub(int i, int j, int[][] board, List<String> memoryList, String subWord){
          if (subWord.length() == 0){
              return true;
          }
          
          if (i < 0 || i > board.size() || j < 0 || j > board[0].size()){
              return false;
          }
          
          if (!board[i][j].equals(subWord[0]) || memoryList.contain(Integer.toString(i) + "," + Integer.toString(j))){
              return false;
          }
          
          boolean a = existSub(i, j+1, board, memoryList.add(Integer.toString(i) + "," + Integer.toString(j)), subWord.subString(1));
          boolean b = existSub(i+1, j, board, memoryList.add(Integer.toString(i) + "," + Integer.toString(j)), subWord.subString(1));
          boolean c = existSub(i, j-1, board, memoryList.add(Integer.toString(i) + "," + Integer.toString(j)), subWord.subString(1));
          boolean d = existSub(i-1, j, board, memoryList.add(Integer.toString(i) + "," + Integer.toString(j)), subWord.subString(1));
          memoryList.removeLast();
          
          if(a || b || c || d){
              return true;
          }
          return false;
      }
  }	
  ```

* 上述代码中存在哪些问题？如何优化代码？

  ```java
  class Solution {
      public boolean exist(char[][] board, String word) {
          List<String> mList = new ArrayList<>();
          int[][] arroundArray = {{0, 1}, {1, 0}, {-1, 0}, {0, -1}};
          for (int i=0; i<board.length; i++){
              for (int j=0; j < board[0].length; j++){
                  if (board[i][j]==word.charAt(0)){
                      mList.add(Integer.toString(i)+","+Integer.toString(j));
                      for (int m = 0; m<arroundArray.length; m++){
                          if(existSub(i+arroundArray[m][0], j+arroundArray[m][1], board, arroundArray, mList, word.substring(1))){
                              return true;
                          }
                      }
                  }
              }
          }
          return false;
      }
  
      private boolean existSub(int i, int j, char[][] board, int[][] arroundArray, List<String> memoryList, String subWord){
          if (subWord.length()==0){
              return true;
          }
          if (i < 0 || i > board.length-1 || j < 0 || j > board[0].length-1){
              return false;
          }
          String s1 = Integer.toString(i)+","+Integer.toString(j);
          if (board[i][j]!=subWord.charAt(0) || memoryList.contains(s1)){
              return false;
          }
  
          memoryList.add(Integer.toString(i)+","+Integer.toString(j));
          for (int m = 0; m<arroundArray.length; m++){
              if(existSub(i+arroundArray[m][0], j+arroundArray[m][1], board, arroundArray, memoryList, subWord.substring(1))){
                  return true;
              }
          }
          memoryList.remove(memoryList.size()-1);
  
          return false;
      }
  }
  ```
  
* 上述代码在输入为：[["C","A","A"],["A","A","A"],["B","C","D"]] "AAB"时，输出为false，预期结果为true。问题出在哪里？如何修改代码？

  ```java
  //分析：在搜索完第一个字母周围的四个字母后，没有将记忆列表中的该元素删掉，当第一个字母周围四个字母都不匹配并移动至下一字母匹配时，导致该字母仍在记忆列表中，导致报错
  class Solution {
      public boolean exist(char[][] board, String word) {
          List<String> mList = new ArrayList<>();
          int[][] arroundArray = {{0, 1}, {1, 0}, {-1, 0}, {0, -1}};
          for (int i=0; i<board.length; i++){
              for (int j=0; j < board[0].length; j++){
                  if (board[i][j]==word.charAt(0)){
                      mList.add(Integer.toString(i)+","+Integer.toString(j));
                      for (int m = 0; m<arroundArray.length; m++){
                          if(existSub(i+arroundArray[m][0], j+arroundArray[m][1], board, arroundArray, mList, word.substring(1))){
                              return true;
                          }
                      }
                      //修改点：在该处将记忆列表中的元素删掉
                      mList.remove(mList.size()-1);
                  }
              }
          }
          return false;
      }
  
      private boolean existSub(int i, int j, char[][] board, int[][] arroundArray, List<String> memoryList, String subWord){
          if (subWord.length()==0){
              return true;
          }
          if (i < 0 || i > board.length-1 || j < 0 || j > board[0].length-1){
              return false;
          }
          String s1 = Integer.toString(i)+","+Integer.toString(j);
          if (board[i][j]!=subWord.charAt(0) || memoryList.contains(s1)){
              return false;
          }
  
          memoryList.add(Integer.toString(i)+","+Integer.toString(j));
          for (int m = 0; m<arroundArray.length; m++){
              if(existSub(i+arroundArray[m][0], j+arroundArray[m][1], board, arroundArray, memoryList, subWord.substring(1))){
                  return true;
              }
          }
          memoryList.remove(memoryList.size()-1);
  
          return false;
      }
  }
  ```

* 上述代码如何优化？

  ```java
  //优化点：可以将对首字母的判断移动到递归函数中，进一步精简代码
  class Solution {
      public boolean exist(char[][] board, String word) {
          List<String> mList = new ArrayList<>();
          int[][] arroundArray = {{0, 1}, {1, 0}, {-1, 0}, {0, -1}};
          for (int i=0; i<board.length; i++){
              for (int j=0; j < board[0].length; j++){
                  if(existSub(i, j, board, arroundArray, mList, word)){
                      return true;
                  }
              }
          }
          return false;
      }
  
      private boolean existSub(int i, int j, char[][] board, int[][] arroundArray, List<String> memoryList, String subWord){
          if (subWord.length()==0){
              return true;
          }
          if (i < 0 || i > board.length-1 || j < 0 || j > board[0].length-1 || board[i][j]!=subWord.charAt(0)){
              return false;
          }
  
          String s1 = Integer.toString(i)+","+Integer.toString(j);
          if (memoryList.contains(s1)){
              return false;
          }
  
          memoryList.add(s1);
          for (int m = 0; m<arroundArray.length; m++){
              if(existSub(i+arroundArray[m][0], j+arroundArray[m][1], board, arroundArray, memoryList, subWord.substring(1))){
                  return true;
              }
          }
          memoryList.remove(memoryList.size()-1);
  
          return false;
      }
  }
  ```

  