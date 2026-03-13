<template>
  <section class="panel tasks">
    <div class="panel-header">
      <h2 class="panel-title">今日任务</h2>
      <div class="progress">
        <div class="progress-bar"></div>
        <span class="progress-text">已完成 3 / 8</span>
      </div>
    </div>

    <ul class="task-list">
      <li class="task-item done">
        <div class="task-left">
          <span class="check"></span>
          <div>
            <p class="task-title">评审线框图</p>
            <p class="task-meta">设计 · 09:30</p>
          </div>
        </div>
        <span class="tag calm">完成</span>
      </li>

      <li class="task-item">
        <div class="task-left">
          <span class="check empty"></span>
          <div>
            <p class="task-title">制作主视觉动效原型</p>
            <p class="task-meta">动效 · 11:00</p>
          </div>
        </div>
        <span class="tag focus">高优先</span>
      </li>

      <li class="task-item">
        <div class="task-left">
          <span class="check empty"></span>
          <div>
            <p class="task-title">撰写空状态文案</p>
            <p class="task-meta">内容 · 14:00</p>
          </div>
        </div>
        <span class="tag neutral">中优先</span>
      </li>

      <li class="task-item blocked">
        <div class="task-left">
          <span class="check empty"></span>
          <div>
            <p class="task-title">与后端团队对齐</p>
            <p class="task-meta">协作 · 等待中</p>
          </div>
        </div>
        <span class="tag alert">受阻</span>
      </li>

      <li class="task-item">
        <div class="task-left">
          <span class="check empty"></span>
          <div>
            <p class="task-title">打磨设置图标</p>
            <p class="task-meta">界面 · 16:30</p>
          </div>
        </div>
        <span class="tag neutral">低优先</span>
      </li>
    </ul>
  </section>
</template>
