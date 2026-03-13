<template>
  <div class="page">
    <header class="hero">
      <div class="hero-badge">今日</div>
      <h1 class="hero-title">专注看板</h1>
      <p class="hero-subtitle">轻量、安静的待办静态布局，方便快速审查。</p>
      <div class="hero-meta">
        <span class="meta-chip">个人</span>
        <span class="meta-chip">第 11 周</span>
        <span class="meta-chip">3 月 13 日</span>
      </div>
    </header>

    <main class="board">
      <section class="panel">
        <div class="panel-header">
          <h2 class="panel-title">添加任务</h2>
          <span class="panel-note">仅静态预览</span>
        </div>

        <div class="task-input">
          <div class="input-field">
            <label class="input-label">任务名称</label>
            <div class="input-box" aria-hidden="true">设计新手引导流程</div>
          </div>
          <div class="input-field">
            <label class="input-label">截止</label>
            <div class="input-box small" aria-hidden="true">3 月 18 日</div>
          </div>
          <button class="primary-btn" type="button">添加任务</button>
        </div>

        <div class="filters">
          <span class="filter active">全部</span>
          <span class="filter">进行中</span>
          <span class="filter">已完成</span>
          <span class="filter">受阻</span>
        </div>
      </section>

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

      <section class="panel notes">
        <div class="panel-header">
          <h2 class="panel-title">备注</h2>
          <span class="panel-note">静态</span>
        </div>
        <div class="note-card">
          <p class="note-title">迭代重点</p>
          <p class="note-body">
            强调清晰、克制的层级关系。保持页面轻量，列表易于扫读，
            并为后续微交互预留空间。
          </p>
        </div>
        <div class="note-card">
          <p class="note-title">快速收益</p>
          <p class="note-body">
            确认任务标签、定稿字体搭配，并让间距对齐看板网格。
          </p>
        </div>
      </section>
    </main>

    <footer class="footer">
      <div class="footer-left">
        <span class="footer-label">状态</span>
        <span class="footer-value">等待设计评审</span>
      </div>
      <button class="ghost-btn" type="button">分享看板</button>
    </footer>
  </div>
</template>
