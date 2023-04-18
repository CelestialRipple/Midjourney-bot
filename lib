"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
exports.apply = exports.Config = exports.name = void 0;
const koishi_1 = require("koishi");
const axios = require('axios');

exports.name = 'midjourney';
exports.Config = koishi_1.Schema.intersect([   
  koishi_1.Schema.object({
      url: koishi_1.Schema.string().description('后端URL'),
      authority: koishi_1.Schema.number().description('允许使用的最低权限').default(0),
        usage: koishi_1.Schema.number().description('每人每日可用次数').default(10),
      }).description('订阅设置'),
])

function hasChineseCharacters(text) {
  return /[\u4e00-\u9fa5]/.test(text);
}

async function apply(ctx , config) {
  ctx
  .command('mj <text:text>', {
        authority: config.authority,
        maxUsage: config.usage
    })
    .action(async ({ session }, text) => {
      // 检查输入中是否包含中文字符
      if (hasChineseCharacters(text)) {
        return "禁止输入中文。";
      }

      try {
        const payload = { "prompt": text };
        const url = config.url;
        const headers = { 'Content-Type': 'application/json' };

        const response = await axios.post(url, payload, { headers });

        // 检查是否存在错误消息
        if (response.data.error) {
          return response.data.error;
        }

        const imageUrl = response.data.latest_image_url;
        const urlresponse = await axios.get(imageUrl, { responseType: 'arraybuffer' });
        const imageBuffer = urlresponse.data;

        if (imageUrl) {
  session.send(koishi_1.segment.quote(session.messageId) + koishi_1.segment.image(imageBuffer));
} else {
  return '没有找到与关键词相关的图片。';
}
      } catch (error) {
        ctx.logger('tools').warn(error);
        return '请求失败。';
      }
    });
}
exports.apply = apply;
