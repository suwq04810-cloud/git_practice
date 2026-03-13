<template>
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
</template>
