<html>
<body>

<hr>
<h2>🪜 一、首次上传项目到 Git 仓库</h2>
<h3>1. 初始化 Git 仓库</h3>
<pre><code class="language-bash">cd 你的项目目录
git init
</code></pre>
<h3>2. 绑定远程仓库地址</h3>
<blockquote>
<p>先去 GitHub / Gitee 上新建一个空仓库（比如：<code inline="">myproject</code>），复制 HTTPS 或 SSH 地址。</p>
</blockquote>
<pre><code class="language-bash">git remote add origin https://github.com/你的用户名/myproject.git
</code></pre>
<p>👉 如果之前已经绑定过远程仓库，重新绑定用：</p>
<pre><code class="language-bash">git remote set-url origin https://github.com/你的用户名/myproject.git
</code></pre>
<h3>3. 添加所有文件并提交</h3>
<pre><code class="language-bash">git add .
git commit -m "🎉 初始化项目"
</code></pre>
<h3>4. 推送到远程</h3>
<p>（如果仓库是空的，一般推送到 <code inline="">main</code> 或 <code inline="">master</code> 分支）</p>
<pre><code class="language-bash">git branch -M main
git push -u origin main
</code></pre>
<p>👉 推送完成后，你刷新 GitHub 页面就能看到项目啦！</p>
<hr>
<h2>📝 二、提交修改后的版本</h2>
<p>比如你修改了一些代码，然后：</p>
<pre><code class="language-bash">git add .
git commit -m "✨ 更新了部分功能"
git push origin main
</code></pre>
<p>此时 Git 历史中就多了一个新的版本。</p>
<hr>
<h2>🕰️ 三、回滚到原始版本（常用两种方式）</h2>
<h3>✅ 方法 1：使用 <code inline="">git log</code> 找到初始提交，然后回滚</h3>
<h4>1. 查看提交历史</h4>
<pre><code class="language-bash">git log --oneline
</code></pre>
<p>输出示例：</p>
<pre><code>a2c9e37 (HEAD -&gt; main, origin/main) ✨ 更新了部分功能
1b6d5d9 🎉 初始化项目
</code></pre>
<p>👉 <code inline="">1b6d5d9</code> 就是初始版本的提交 ID。</p>
<h4>2. 回滚到初始版本（临时回退）</h4>
<pre><code class="language-bash">git checkout 1b6d5d9
</code></pre>
<p>此时项目文件已经变成最初版本的样子，但处于“游离状态（detached HEAD）”。</p>
<h4>3. 如果你想“正式回退”并覆盖当前分支：</h4>
<pre><code class="language-bash">git reset --hard 1b6d5d9
git push -f origin main
</code></pre>
<p>⚠️ 这个操作会<strong>清除之后的提交记录</strong>，相当于彻底回到原始版本！</p>
<hr>
<h3>🪄 方法 2：使用 <code inline="">git revert</code> 逆向提交</h3>
<p>如果不想删除历史，只是撤销这次更新（保留记录）：</p>
<pre><code class="language-bash">git revert a2c9e37
</code></pre>
<p>这会生成一个新的提交，把 <code inline="">a2c9e37</code> 的改动逆向撤销。然后推送：</p>
<pre><code class="language-bash">git push origin main
</code></pre>
<p>👉 这种方式更安全，适合团队协作。</p>
<hr>
<h2>🧼 四、附加：常用 Git 命令总结</h2>

命令 | 作用
-- | --
git status | 查看当前改动状态
git add . | 添加所有改动到暂存区
git commit -m "说明" | 提交改动
git push origin main | 推送改动到远程
git log --oneline | 查看简洁的历史记录
git reset --hard <id> | 强制回滚到某个版本
git revert <id> | 逆向撤销某个提交
git checkout <id> | 查看历史版本（不改动当前分支）


<hr>
<p>要不要我直接用你的项目名和 GitHub 示例仓库名，帮你写一整套上传+回滚的命令（可直接复制）？🚀</p></body></html><!--EndFragment-->
</body>
</html>