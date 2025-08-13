<script lang="ts" setup>
import type { CalendarDateType, CalendarInstance } from 'element-plus'
import { ArrowLeftBold, ArrowRightBold, Calendar, Clock, DArrowLeft, DArrowRight } from '@element-plus/icons-vue'
import { ref } from 'vue'

const calendar = ref<CalendarInstance>()
const dateRange = ref(['', ''])
const selectingStart = ref(true) // 用于跟踪当前选择的是开始还是结束日期

// 检查日期是否在选中范围内
function isDateInRange(dateStr: string) {
  if (!dateRange.value[0] || !dateRange.value[1])
    return false
  const date = new Date(dateStr)
  const start = new Date(dateRange.value[0])
  const end = new Date(dateRange.value[1])
  return date >= start && date <= end
}

function formatYearMonth(date: string): string {
  // 使用正则表达式提取年份和月份
  const matches = date.match(/(\d{4})\s*年\s*(\d{1,2})\s*月/)
  if (!matches) {
    throw new Error('输入格式不正确')
  }

  const year = matches[1] // 年份
  let month = matches[2] // 月份

  // 确保月份是两位数
  month = month.padStart(2, '0')

  // 返回格式化后的日期字符串
  return `${year}-${month}`
}

function selectDate(val: CalendarDateType) {
  if (!calendar.value)
    return
  calendar.value.selectDate(val)
}

// 处理日历日期点击
function handleDateClick(date: Date) {
  const dateStr = formatDate(date)

  if (selectingStart.value) {
    // 选择开始日期
    dateRange.value = [dateStr, '']
    selectingStart.value = false
  }
  else {
    // 选择结束日期
    if (dateStr < dateRange.value[0]) {
      // 如果选择的日期比开始日期早，交换它们
      dateRange.value = [dateStr, dateRange.value[0]]
    }
    else {
      dateRange.value = [dateRange.value[0], dateStr]
    }
    selectingStart.value = true
  }
}

// 格式化日期为 YYYY-MM-DD
function formatDate(date: Date | string): string {
  const d = new Date(date)
  const year = d.getFullYear()
  const month = String(d.getMonth() + 1).padStart(2, '0')
  const day = String(d.getDate()).padStart(2, '0')
  return `${year}-${month}-${day}`
}
// 重置选择
function resetSelection() {
  dateRange.value = ['', '']
  selectingStart.value = true
}
</script>

<template>
  <div class="outContainer">
    <div>
      <div class="calendar-title-container">
        <div class="timeText">
          时间
        </div>
        <div class="timePeriod">
          <div class="timeperiodText flex">
            <div class="h-[28px] w-[110px]">
              {{ dateRange[0] }}
            </div>
            &nbsp;至&nbsp;
            <div class="28px w-[110px]">
              {{ dateRange[1] }}
            </div>
          </div>

          <el-icon style="width: 18px;height: 28px;color: #ffffff">
            <Calendar />
          </el-icon>
        </div>
        <el-button
          :icon="Clock" class="clock_container" title="重置选择"
          @click="resetSelection"
        />
      </div>
      <el-calendar ref="calendar" @select="handleDateClick">
        <template #header="{ date }">
          <!--          <el-button-group> -->
          <div class="h-[28px] w-full flex justify-between">
            <div>
              <el-icon class="arrowStyle" @click="selectDate('prev-year')">
                <DArrowLeft />
              </el-icon>
              <el-icon class="arrowStyle" @click="selectDate('prev-month')">
                <ArrowLeftBold />
              </el-icon>
            </div>
            <div
              class="date-text"
            >
              {{ formatYearMonth(date) }}
            </div>

            <div>
              <el-icon class="arrowStyle" @click="selectDate('next-year')">
                <ArrowRightBold />
              </el-icon>
              <el-icon class="arrowStyle" @click="selectDate('next-month')">
                <DArrowRight />
              </el-icon>
            </div>
          </div>
          <!--          </el-button-group> -->
        </template>
        <template #date-cell="{ data }">
          <div
            :class="{
              'in-range': isDateInRange(data.day),
              'range-start': formatDate(data.day) === dateRange[0],
              'range-end': formatDate(data.day) === dateRange[1],
              'range-single': dateRange[0] && dateRange[1] && dateRange[0] === dateRange[1] && formatDate(data.day) === dateRange[0],
            }"
            @click="handleDateClick(new Date(data.day))"
          >
            {{ new Date(data.day).getDate() }}
          </div>
        </template>
      </el-calendar>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.outContainer {
  display: flex;
  width: 410px;
  padding: 24px;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  border-radius: 28px;
  border: 0.575px solid #eaeaea;
  background: linear-gradient(180deg, rgba(170, 193, 209, 0.21) 13.05%, rgba(229, 243, 254, 0.3) 100%);
  box-shadow:
    0 4.603px 23.016px 0 rgba(0, 0, 0, 0.15),
    0 0 5.754px 0 rgba(255, 255, 255, 0.25) inset;
  backdrop-filter: blur(20px);
}

.calendar-title-container {
  display: flex;
  padding: 21.121px 0;
  align-items: center;
  gap: 10px;
}

.timeText {
  color: #fff;
  font-family: YouSheBiaoTiHei;
  font-size: 19.801px;
  font-style: normal;
  font-weight: 400;
  line-height: 120%; /* 23.761px */
}

.timePeriod {
  display: flex;
  width: 270px;
  padding: 0 10px 0 11px;
  justify-content: space-between;
  align-items: center;
  border-radius: 5px;
  border: 1px solid #fff;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(24.751081466674805px);
}

.timeperiodText {
  width: 211px;
  height: 28px;
  color: #fff;
  font-family: SourceHanSansCN;
  font-size: 17px;
  font-style: normal;
  font-weight: 400;
  line-height: 28px; /* 217.783% */
}

.CalendarContainer {
  width: 11.881px;
  height: 11.881px;
}

.clock_container {
  color: #ffffff;
  width: 28px;
  height: 28px;
  border-radius: 5px;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(25px);
}

.ep-calendar {
  width: 343px;
  height: 251px;
  background: linear-gradient(357deg, rgba(75, 177, 200, 0.9) 8.01%, #3ae2f7 132.26%, #27b9f5 132.51%) !important;
  border: 1px solid #10fcff;
  border-radius: 6px;
  filter: drop-shadow(2.97px 2.97px 3.96px rgba(0, 0, 0, 0.16));
  --ep-calendar-border: none;
  --ep-calendar-cell-width: none;
  --ep-calendar-selected-bg-color: rgba(40, 104, 136, 0.3);

  :deep(.ep-calendar__body) {
    padding-top: 0 !important;
  }

  /*日历标题*/
  :deep(.ep-calendar-table thead th) {
    color: #fff;
    text-align: center;
    font-family: 'Source Han Sans CN';
    font-size: 14.851px;
    font-style: normal;
    font-weight: 500;
    line-height: 10.89px; /* 73.333% */
  }

  :deep(.ep-calendar-day) {
    padding: 0 !important;
  }
}

.ep-button {
  padding: 8px 8px;
  float: none;
  --ep-button-border-color: transparent;
  --ep-button-bg-color: none;
}

.date-text {
  color: #fff;
  font-family: Source Han Sans CN;
  font-size: 16px;
  font-style: normal;
  font-weight: 700;
  line-height: 28px;
  white-space: nowrap; /* 禁止换行 */
}

.arrowStyle {
  width: 28px;
  height: 28px;

  :deep(svg) {
    font-size: 18px;
    color: #0ff9fc;
    font-weight: bold;
  }
}

/* 非本月日期弱化样式 */
:deep(.ep-calendar-table:not(.is-range) td.prev),
:deep(.ep-calendar-table:not(.is-range) td.next) {
  color: rgba(255, 255, 255, 0.4);
  text-align: center;
  font-family: SourceHanSansCN;
  font-size: 15px;
  font-style: normal;
  font-weight: 500;
  line-height: 24px; /* 73.333% */
}

:deep(.ep-calendar-table tr:first-child td),
:deep(.ep-calendar-table tr td:first-child),
:deep(.ep-calendar-table td) {
  color: #fff;
  text-align: center;
  font-family: SourceHanSansCN;
  font-size: 15px;
  font-style: normal;
  font-weight: 500;
  line-height: 24px; /* 73.333% */
}

:deep(.ep-calendar-table) {
  padding: 0 !important;
}

:deep(.ep-calendar-day):active,
:deep(.ep-calendar-day):hover {
  background: none;
}

:deep(.ep-calendar-day):hover {
  background: none;
}

.in-range {
  background: rgba(40, 104, 136, 0.3);
}

/* 开始/结束日期样式 */
.range-start,
.range-end {
  position: relative;
  background: rgba(0, 255, 251, 0.2);
  box-shadow:
    0 0 5px #00fffb,
    0 0 15px #00fffb,
    inset 0 0 10px #00fffb;
  border: 1px solid #00fffb !important;

  &::before {
    content: '';
    position: absolute;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    border: 1px solid #00fffb;
    z-index: -1;
  }
}

/* 当只有一个日期被选中时的样式 */
.range-single {
  position: relative;
  background: rgba(0, 255, 251, 0.2);
  box-shadow:
    0 0 5px #00fffb,
    0 0 15px #00fffb,
    inset 0 0 10px #00fffb;
  border: 1px solid #00fffb !important;
}
</style>
