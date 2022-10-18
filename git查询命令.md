## 注意：要在git bash内使用
因为Windows powershell无法识别管道符

## 查看个人提交代码行数
    git log --author="longhaoyue" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "add lines: %s, removed lines: %s\n", add, subs }' -

## 查看仓库内总提交次数
    git log --oneline | wc -l 

## 查看仓库内每个人的提交次数
    git log --pretty='%aN' | sort | uniq -c | sort -k1 -n -r

https://blog.csdn.net/carterslam/article/details/81162463

https://blog.csdn.net/qq_42301302/article/details/115489995