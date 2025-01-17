# 版权所有 (c) 2025 [作业借你抄 @chaozuoye ]
#推特主页连接： x.com/chaozuoye
#
# 本代码基于 MIT 许可证开源发布。
# 您可以自由使用、复制、修改、合并、发布、分发、再许可和/或销售本代码的副本，
# 但须遵守以下条件：
#
# 1. 在任何分发或衍生作品中，必须明确提及原作者 ([作业借你抄) 并给予适当的署名。
# 2. 在分发代码或衍生作品时，必须包含本许可的完整内容。
# 
# 本代码按“现状”提供，不附带任何明示或暗示的担保，包括但不限于
# 对特定用途的适用性或适销性的担保。


// 配置项
const maxClicks = 10000; // 总最大点击次数
const minDelay = 1000; // 每次点击的最小延迟（毫秒）
const maxDelay = 3000; // 每次点击的最大延迟（毫秒）
const clickDurationRange = [600, 1200]; // 每次点击的持续时间范围（秒）
const pauseDurationRange = [600, 1800]; // 点击暂停时间范围（秒）

// 全局变量
let isRunning = true; // 全局开关
let clickCount = 0; // 当前点击次数

// 打印欢迎信息
function printWelcomeMessage() {
    console.log("欢迎使用随机点击程序！");
    console.log("作者推特：Twitter: @chaozuoye");
    console.log("主页链接欢迎关注: https://x.com/chaozuoye");
    console.log("程序即将开始运行...\n");
}

//如果你想删除上述署名信息，请@作者 x.com/chaozuoye ，我会很开心并帮助你宣传！！！

// 格式化时间为 YYYY-MM-DD HH:mm:ss
function getCurrentTime() {
    const now = new Date();
    const year = now.getFullYear();
    const month = String(now.getMonth() + 1).padStart(2, "0");
    const day = String(now.getDate()).padStart(2, "0");
    const hours = String(now.getHours()).padStart(2, "0");
    const minutes = String(now.getMinutes()).padStart(2, "0");
    const seconds = String(now.getSeconds()).padStart(2, "0");
    return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
}

// 获取随机时间（秒）
function getRandomTime(range) {
    const [min, max] = range;
    return Math.random() * (max - min) + min; // 返回范围内的随机值
}

// 随机点击函数
function randomCenterLeftClick() {
    // 检查是否需要停止运行
    if (!isRunning || clickCount >= maxClicks) {
        console.log("到达最大点击次数，程序已停止运行。");
        return;
    }

    // 获取屏幕尺寸
    const screenWidth = window.innerWidth || document.documentElement.clientWidth;
    const screenHeight = window.innerHeight || document.documentElement.clientHeight;

    // 定义中间偏左10%区域
    const marginRatio = 0.1; // 边距比例（10%）
    const minX = screenWidth * marginRatio; // 左边距
    const maxX = screenWidth * 0.5; // 中间线
    const minY = screenHeight * marginRatio; // 上边距
    const maxY = screenHeight * (1 - marginRatio); // 下边距

    // 随机生成点击位置（中间偏左10%区域内）
    const randomX = () => Math.floor(minX + Math.random() * (maxX - minX));
    const randomY = () => Math.floor(minY + Math.random() * (maxY - minY));
    const x = randomX();
    const y = randomY();

    // 创建并触发点击事件
    const clickEvent = new MouseEvent("click", {
        view: window,
        bubbles: true,
        cancelable: true,
        clientX: x,
        clientY: y
    });
    const targetElement = document.elementFromPoint(x, y);
    if (targetElement) {
        targetElement.dispatchEvent(clickEvent);
    }

    // 记录点击
    clickCount++;
    const currentFormattedTime = getCurrentTime();

    // 输出调试信息
    console.log(
        `点击位置: (${x}, ${y}), 当前点击次数: ${clickCount}, 当前时间: ${currentFormattedTime}`
    );

    // 安排下一次点击
    const delay = minDelay + Math.random() * (maxDelay - minDelay);
    setTimeout(randomCenterLeftClick, delay);
}

// 主程序：运行点击
function startClicking() {
    console.log("程序开始运行...");

    // 每次点击持续的随机时间（毫秒）
    const clickDuration = getRandomTime(clickDurationRange) * 1000;

    function runClicking() {
        const startTime = Date.now();

        // 持续点击逻辑
        const intervalId = setInterval(() => {
            const elapsedTime = Date.now() - startTime;

            // 如果达到点击持续时间或点击次数已满，则暂停
            if (elapsedTime >= clickDuration || clickCount >= maxClicks || !isRunning) {
                clearInterval(intervalId); // 停止持续点击
                if (!isRunning || clickCount >= maxClicks) {
                    console.log("点击已停止。");
                    return;
                }

                // 随机暂停时间
                const pauseDuration = getRandomTime(pauseDurationRange) * 1000;
                console.log(
                    `本轮点击结束，将暂停 ${pauseDuration / 1000} 秒后重新开始...`
                );

                // 在暂停时间后重新启动点击
                setTimeout(startClicking, pauseDuration);
            } else {
                randomCenterLeftClick();
            }
        }, minDelay); // 每次点击的最小延迟
    }

    // 开始本轮点击
    runClicking();
}

// 停止程序的方法
function stopClicking() {
    isRunning = false;
    console.log("程序将在本轮点击完成后停止。");
}

// 启动程序
printWelcomeMessage(); // 打印推特信息
startClicking();
