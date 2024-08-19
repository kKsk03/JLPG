<template>
    <div class="container" style="margin-top: 30px;">
        <div class="row row-cols-1 row-cols-lg-2 g-2 g-lg-3">
            <!-- 预览框 -->
            <div class="col">
                <div class="preview">
                    <canvas ref="canvas" width="237" height="122"></canvas>
                </div>
            </div>
            <!-- 操作区域 -->
            <div class="col">
                <!-- 地区名 -->
                <div class="input-group mb-3">
                    <label class="input-group-text">地区名</label>
                    <select class="form-select" id="inputGroupSelect_Regions" v-model="selectedRegion">
                        <option v-for="region in optionalRegions" :value="region" :key="region">{{ region }}</option>
                    </select>
                </div>
                <!-- 车牌号（大数字） -->
                <div class="input-group mb-3">
                    <label class="input-group-text">车牌号</label>
                    <select class="form-select" id="inputGroupSelect_BigNumber_01" v-model="selectedBigNumber01">
                        <option v-for="number in optionalBigNumbers" :value="number" :key="number">{{ number }}</option>
                    </select>
                    <select class="form-select" id="inputGroupSelect_BigNumber_02" v-model="selectedBigNumber02">
                        <option v-for="number in optionalBigNumbers" :value="number" :key="number">{{ number }}</option>
                    </select>
                    <select class="form-select" id="inputGroupSelect_BigNumber_Mid" v-model="selectedBigNumberMid">
                        <option v-for="number in optionalBigNumbersMid" :value="number" :key="number">{{ number }}
                        </option>
                    </select>
                    <select class="form-select" id="inputGroupSelect_BigNumber_03" v-model="selectedBigNumber03">
                        <option v-for="number in optionalBigNumbers" :value="number" :key="number">{{ number }}</option>
                    </select>
                    <select class="form-select" id="inputGroupSelect_BigNumber_04" v-model="selectedBigNumber04">
                        <option v-for="number in optionalBigNumbers" :value="number" :key="number">{{ number }}</option>
                    </select>
                </div>
                <!-- 分类编号 -->
                <div class="input-group mb-3">
                    <label class="input-group-text">分类号</label>
                    <select class="form-select" id="inputGroupSelect_SmallNumber_01" v-model="selectedSmallNumber01">
                        <option v-for="number in optionalSmallNumbers" :value="number" :key="number">{{ number }}
                        </option>
                    </select>
                    <select class="form-select" id="inputGroupSelect_BigNumber_02" v-model="selectedSmallNumber02">
                        <option v-for="number in optionalSmallNumbers" :value="number" :key="number">{{ number }}
                        </option>
                    </select>
                    <select class="form-select" id="inputGroupSelect_BigNumber_03" v-model="selectedSmallNumber03">
                        <option v-for="number in optionalSmallNumbers" :value="number" :key="number">{{ number }}
                        </option>
                    </select>
                </div>
                <!-- 平假名 -->
                <div class="input-group mb-3">
                    <label class="input-group-text">平假名</label>
                    <select class="form-select" id="inputGroupSelect_Hiragana_01" v-model="selectedHiragana">
                        <option v-for="hiragana in optionalHiragana" :value="hiragana" :key="hiragana">{{ hiragana }}
                        </option>
                    </select>
                </div>
                <div class="d-grid gap-1 d-md-flex justify-content-md">
                    <button class="btn btn-primary" style="width: 100%;" type="button" @click="drawPlate()">预览</button>
                    <button class="btn btn-success" style="width: 100%;" type="button" @click="generateImage()">生成</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            // 可供选择变量
            optionalBigNumbers: ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "·"],
            optionalBigNumbersMid: ["-", "无"],
            optionalSmallNumbers: ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0", "无"],
            optionalRegions: ["いわき", "つくば", "とちぎ", "なにわ", "一宮", "三河", "三重", "上越", "下関", "世田谷", "久留米", "京都", "仙台", "伊勢志摩", "伊豆", "会津", "佐世保", "佐倉", "佐賀", "倉敷", "八戸", "八王子", "出雲", "函館", "前橋", "北九州", "北見", "千葉", "名古屋", "和歌山", "和泉", "品川", "四日市", "土浦", "堺", "多摩", "大分", "大宮", "大阪", "奄美", "奈良", "姫路", "宇都宮", "室蘭", "宮城", "宮崎", "富士山", "富山", "尾張小牧", "山口", "山梨", "岐阜", "岡山", "岡崎", "岩手", "島根", "川口", "川崎", "川越", "市原", "市川", "帯広", "平泉", "広島", "庄内", "弘前", "徳島", "愛媛", "成田", "所沢", "新潟", "旭川", "春日井", "春日部", "札幌", "杉並", "東美濃", "松戸", "松本", "板橋", "柏", "横浜", "水戸", "江東", "沖縄", "沼津", "浜松", "渡良濑", "湘南", "滋賀", "熊本", "熊谷", "町田", "白河", "盛岡", "相模", "知床", "石川", "神戸", "福井", "福山", "福岡", "福島", "秋田", "筑豊", "練馬", "群馬", "習志野", "苫小牧", "葛飾", "袖ヶ浦", "諏訪", "豊橋", "豊田", "越谷", "足立", "那須", "郡山", "野田", "金沢", "釧路", "鈴鹿", "長岡", "長崎", "長野", "雪国鱼沼", "青森", "静岡", "飛騨", "香川", "高崎", "高松", "高知", "鳥取", "鹿児島"],
            optionalHiragana: ["さ", "す", "せ", "そ", "た", "ち", "つ", "て", "と", "な", "に", "ぬ", "ね", "の", "は", "ひ", "ふ", "ほ", "ま", "み", "む", "め", "も", "や", "ゆ", "ら", "り", "る", "ろ"],
            // 选择变量
            selectedRegion: null, // 地区
            selectedBigNumber01: null, // 大数字（第一位）
            selectedBigNumber02: null, // 大数字（第二位）
            selectedBigNumberMid: null, // 大数字（中间）
            selectedBigNumber03: null, // 大数字（第三位）
            selectedBigNumber04: null, // 大数字（第四位）
            selectedSmallNumber01: null, // 小数字（第一位）
            selectedSmallNumber02: null, // 小数字（第二位）
            selectedSmallNumber03: null, // 小数字（第三位）
            selectedHiragana: null, // 平假名
        }
    },
    methods: {
        async drawPlate() {
            const canvas = this.$refs.canvas;
            const ctx = canvas.getContext("2d");

            // 清除画布
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // 绘制地区
            const regionModule = await import(`@/assets/regions/${this.selectedRegion}.png`);
            const regionImage = new Image();
            regionImage.src = regionModule.default;
            regionImage.onload = () => {
                ctx.drawImage(regionImage, 55, 10, 65, 32);
            };

            // 绘制平假名
            const hiraganaModule = await import(`@/assets/hiragana/${this.selectedHiragana}.png`);
            const hiraganaImage = new Image();
            hiraganaImage.src = hiraganaModule.default;
            hiraganaImage.onload = () => {
                ctx.drawImage(hiraganaImage, 10, 70, 33, 34);
            };

            // 绘制车牌号（第一位）
            const bigNumber01Module = await import(`@/assets/numbers/big/${this.selectedBigNumber01}.png`);
            const bigNumber01Image = new Image();
            bigNumber01Image.src = bigNumber01Module.default;
            bigNumber01Image.onload = () => {
                ctx.drawImage(bigNumber01Image, 50, 55);
            };

            // 绘制车牌号（第二位）
            const bigNumber02Module = await import(`@/assets/numbers/big/${this.selectedBigNumber02}.png`);
            const bigNumber02Image = new Image();
            bigNumber02Image.src = bigNumber02Module.default;
            bigNumber02Image.onload = () => {
                ctx.drawImage(bigNumber02Image, 90, 55);
            };

            // 绘制车牌号（中间）
            const dashModule = await import(`@/assets/numbers/big/${this.selectedBigNumberMid}.png`);
            const dashImage = new Image();
            dashImage.src = dashModule.default;
            dashImage.onload = () => {
                ctx.drawImage(dashImage, 130, 55);
            };

            // 绘制车牌号（第三位）
            const bigNumber03Module = await import(`@/assets/numbers/big/${this.selectedBigNumber03}.png`);
            const bigNumber03Image = new Image();
            bigNumber03Image.src = bigNumber03Module.default;
            bigNumber03Image.onload = () => {
                ctx.drawImage(bigNumber03Image, 150, 55);
            };

            // 绘制车牌号（第四位）
            const bigNumber04Module = await import(`@/assets/numbers/big/${this.selectedBigNumber04}.png`);
            const bigNumber04Image = new Image();
            bigNumber04Image.src = bigNumber04Module.default;
            bigNumber04Image.onload = () => {
                ctx.drawImage(bigNumber04Image, 190, 55);
            };

            // 绘制分类号（第1位）
            const smallNumber01Module = await import(`@/assets/numbers/small/${this.selectedSmallNumber01}.png`);
            const smallNumber01Image = new Image();
            smallNumber01Image.src = smallNumber01Module.default;
            smallNumber01Image.onload = () => {
                ctx.drawImage(smallNumber01Image, 125, 10);
            };

            // 绘制分类号（第2位）
            const smallNumber02Module = await import(`@/assets/numbers/small/${this.selectedSmallNumber02}.png`);
            const smallNumber02Image = new Image();
            smallNumber02Image.src = smallNumber02Module.default;
            smallNumber02Image.onload = () => {
                ctx.drawImage(smallNumber02Image, 145, 10);
            };

            // 绘制分类号（第3位）
            const smallNumber03Module = await import(`@/assets/numbers/small/${this.selectedSmallNumber03}.png`);
            const smallNumber03Image = new Image();
            smallNumber03Image.src = smallNumber03Module.default;
            smallNumber03Image.onload = () => {
                ctx.drawImage(smallNumber03Image, 165, 10);
            };
        },
        generateImage() {
            const canvas = this.$refs.canvas;
            const link = document.createElement("a");
            link.href = canvas.toDataURL("image/png");
            link.download = "license_plate.png";
            link.click();
        },
    },
    mounted() {
        // 预设
        this.selectedRegion = "多摩";
        this.selectedHiragana = "さ";
        this.selectedBigNumber01 = "4";
        this.selectedBigNumber02 = "6";
        this.selectedBigNumberMid = "-";
        this.selectedBigNumber03 = "4";
        this.selectedBigNumber04 = "9";
        this.selectedSmallNumber01 = "3";
        this.selectedSmallNumber02 = "0";
        this.selectedSmallNumber03 = "0";
    }
}
</script>

<style scoped>
canvas {
    border: 1px solid #ccc;
}
</style>