<template>
  <div class="yugioh-card-container">
    <div class="yugioh-card">
      <div ref="card" class="card" />
    </div>
    <div class="form">
      <div class="form-header">
        <div class="form-title">
          <span>游戏王卡片 - Yugioh Card</span>
          <Icon
            class="github-icon"
            icon="ri:github-fill"
            width="24"
            height="24"
            @click="toGithub"
          />
        </div>
        <div class="form-description">
          <span>一个使用 Canvas 渲染游戏王卡片的工具</span>
        </div>
      </div>

      <div class="form-main">
        <el-form :model="form" label-width="auto">
          <el-row :gutter="gutter">
            <el-col :span="20">
              <el-form-item label="卡片">
                <el-select
                  v-model="form.card"
                  placeholder="请选择卡片"
                  @change="changeCard"
                >
                <el-option label="游戏王" value="yugioh" />
                  <el-option label="超速决斗" value="rush-duel" />
                  <el-option label="游戏王卡背" value="yugioh-back" />
                  <el-option label="场地中心卡" value="field-center" />
                  <el-option label="游戏王 2 期" value="yugioh-series-2" />
                </el-select>
              </el-form-item>
            </el-col>
            <el-col :span="4">
              <el-form-item label="UI">
                <el-switch v-model="form.ui" />
              </el-form-item>
            </el-col>
          </el-row>

          <div v-if="form.card === 'yugioh' && form.ui === true">
            <el-form-item label="语言">
              <el-select v-model="form.data.language" placeholder="请选择语言" @change="changeLanguageYugioh">
                <el-option label="简体中文" value="sc" />
                <el-option label="繁体中文" value="tc" />
                <el-option label="日文" value="jp" />
                <el-option label="韩文" value="kr" />
                <el-option label="英文" value="en" />
                <el-option label="星光界文" value="astral" />
              </el-select>
            </el-form-item>
            <el-form-item label="卡名">
              <el-input v-model="form.data.name" placeholder="请输入卡名" />
            </el-form-item>
            <el-form-item label="对齐">
              <el-radio-group v-model="form.data.align">
                <el-radio-button label="left">
                  <i class="fa-solid fa-align-left" />
                </el-radio-button>
                <el-radio-button label="center">
                  <i class="fa-solid fa-align-center" />
                </el-radio-button>
                <el-radio-button label="right">
                  <i class="fa-solid fa-align-right" />
                </el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="类型">
              <el-radio-group v-model="form.data.type">
               <el-radio-button label="monster">怪兽</el-radio-button>
                <el-radio-button label="spell">魔法</el-radio-button>
                <el-radio-button label="trap">陷阱</el-radio-button>
                <el-radio-button label="pendulum">灵摆</el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="['monster','pendulum'].includes(form.data.type)" label="属性">
              <el-radio-group v-model="form.data.attribute" class="attribute-radio-group">
                <el-radio-button label="dark">暗</el-radio-button>
                <el-radio-button label="light">光</el-radio-button>
                <el-radio-button label="earth">地</el-radio-button>
                <el-radio-button label="water">水</el-radio-button>
                <el-radio-button label="fire">炎</el-radio-button>
                <el-radio-button label="wind">风</el-radio-button>
                <el-radio-button label="divine">神</el-radio-button>
                <el-radio-button label="">无</el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="['spell','trap'].includes(form.data.type)" label="图标">
              <el-select v-model="form.data.icon" placeholder="请选择图标" clearable>
                <el-option label="装备" value="equip" />
                <el-option label="场地" value="filed" />
                <el-option label="速攻" value="quick-play" />
                <el-option label="仪式" value="ritual" />
                <el-option label="永续" value="continuous" />
                <el-option label="反击" value="counter" />
              </el-select>
            </el-form-item>
            <el-form-item label="图片">
              <el-space :size="10">
                <el-upload
                  action="/"
                  :show-file-list="false"
                  accept="image/*"
                  :before-upload="beforeUploadCard"
                >
                  <el-button type="primary">选择图片</el-button>
                </el-upload>
                <el-button plain @click="deleteImage">删除</el-button>
              </el-space>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="卡类">
              <el-select v-model="form.data.cardType" placeholder="请选择卡类">
                <el-option label="通常" value="normal" />
                <el-option label="效果" value="effect" />
                <el-option label="仪式" value="ritual" />
                <el-option label="融合" value="fusion" />
                <el-option label="同调" value="synchro" />
                <el-option label="超量" value="xyz" />
                <el-option label="连接" value="link" />
                <el-option label="衍生物" value="token" />
              </el-select>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'pendulum'" label="灵摆">
              <el-select v-model="form.data.pendulumType" placeholder="请选择灵摆">
                <el-option label="通常 / 灵摆" value="normal-pendulum" />
                <el-option label="效果 / 灵摆" value="effect-pendulum" />
                <el-option label="仪式 / 灵摆" value="ritual-pendulum" />
                <el-option label="融合 / 灵摆" value="fusion-pendulum" />
                <el-option label="同调 / 灵摆" value="synchro-pendulum" />
                <el-option label="超量 / 灵摆" value="xyz-pendulum" />
              </el-select>
            </el-form-item>
            <el-form-item v-if="(form.data.type === 'monster' && ['normal', 'effect', 'ritual', 'fusion', 'synchro', 'token'].includes(form.data.cardType)) || (form.data.type === 'pendulum' && ['normal-pendulum', 'effect-pendulum', 'ritual-pendulum', 'fusion-pendulum', 'synchro-pendulum'].includes(form.data.pendulumType))" label="星级">
              <el-input-number
                v-model="form.data.level"
                :min="0"
                :max="13"
                :precision="0"
              />
            </el-form-item>
            <el-form-item v-if="(form.data.type === 'monster' && form.data.cardType === 'xyz') || (form.data.type === 'pendulum' && form.data.pendulumType === 'xyz-pendulum')" label="阶级">
              <el-input-number
                v-model="form.data.rank"
                :min="0"
                :max="13"
                :precision="0"
              />
            </el-form-item>
            <el-form-item v-if="form.data.type === 'pendulum'" label="刻度">
              <el-input-number
                v-model="form.data.pendulumScale"
                :min="0"
                :max="13"
                :precision="0"
              />
            </el-form-item>
            <el-form-item v-if="form.data.type === 'pendulum'" label="灵摆效果" label-width="40px">
              <el-input
                v-model="form.data.pendulumDescription"
                type="textarea"
                :autosize="{minRows: 3}"
                placeholder="请输入灵摆效果"
              />
            </el-form-item>
            <el-form-item v-if="['monster','pendulum'].includes(form.data.type)" label="种族">
              <el-input v-model="form.data.monsterType" placeholder="请输入种族" />
            </el-form-item>
            <el-form-item v-if="['monster','pendulum'].includes(form.data.type)" label="攻守">
              <el-switch v-model="form.data.atkBar"/>
            </el-form-item>
            <el-form-item v-if="['monster','pendulum'].includes(form.data.type) && form.data.atkBar ===   true" label="ATK">
              <el-input-number
                v-model="form.data.atk"
                :min="-2"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（?：-1，∞：-2）</span>
            </el-form-item>
            <el-form-item v-if="(form.data.type === 'monster' && form.data.cardType !== 'link' && form.data.atkBar === true) || (form.data.type === 'pendulum' && form.data.atkBar === true)" label="DEF">
              <el-input-number
              v-model="form.data.def"
                :min="-2"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（?：-1，∞：-2）</span>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster' && form.data.cardType === 'link'" label="箭头">
              <div class="arrow-form">
                <div
                  v-for="item in [8,1,2,7,9,3,6,5,4]"
                  class="arrow-item"
                  :style="arrowItemStyle(item)"
                  @click="toggleArrow(item)"
                >
                  <i v-if="item === 1" class="fa-solid fa-up" />
                  <i v-if="item === 2" class="fa-solid fa-up-right" />
                  <i v-if="item === 3" class="fa-solid fa-right" />
                  <i v-if="item === 4" class="fa-solid fa-down-right" />
                  <i v-if="item === 5" class="fa-solid fa-down" />
                  <i v-if="item === 6" class="fa-solid fa-down-left" />
                  <i v-if="item === 7" class="fa-solid fa-left" />
                  <i v-if="item === 8" class="fa-solid fa-up-left" />
                </div>
              </div>
            </el-form-item>
            <el-form-item label="效果">
              <el-row :gutter="gutter">
              <el-col :span="12">
                <el-switch v-model="form.data.firstLineCompress" active-text="首行压缩" />
              </el-col>
              <el-col :span="12">
                <el-switch v-model="form.data.descriptionAlign" active-text="文本居中" />
              </el-col>
            </el-row>
              <el-input
                v-model="form.data.description"
                style="margin-top: 10px"
                type="textarea"
                :autosize="{minRows: 3}"
                placeholder="请输入效果"
              />
            </el-form-item>
            <el-form-item label="字号">
              <el-slider
                v-model="form.data.descriptionZoom"
                :min="0.5"
                :max="1.5"
                :step="0.02"
                :marks="descriptionZoomMarks"
              />
            </el-form-item>
            <el-form-item label="卡包">
              <el-input v-model="form.data.package" placeholder="请输入卡包" />
            </el-form-item>
            <el-form-item label="密码">
                <el-input v-model="form.data.password" placeholder="请输入密码" />
            </el-form-item>
            <el-form-item label="版权">
              <el-select
                v-model="form.data.copyright"
                placeholder="请选择版权"
                clearable
                fit-input-width
              >
                <el-option label="©2020 Studio Dice/SHUEISHA, TV TOKYO, KONAMI" value="sc" />
                <el-option label="©スタジオ・ダイス/集英社・テレビ東京・KONAMI" value="jp" />
                <el-option label="©1996 KAZUKI TAKAHASHI" value="en" />
              </el-select>
            </el-form-item>
            <el-form-item label="罕贵">
              <el-select
                v-model="form.data.rare"
                placeholder="请选择罕贵"
                clearable
              >
                <el-option label="DT" value="dt" />
                <el-option label="UR" value="ur" />
                <el-option label="GR" value="gr" />
                <el-option label="HR" value="hr" />
                <el-option label="SER" value="ser" />
                <el-option label="GSER" value="gser" />
                <el-option label="PSER" value="pser" />
              </el-select>
            </el-form-item>
            <el-form-item label="角标">
              <el-select
                v-model="form.data.laser"
                placeholder="请选择角标"
                clearable
              >
                <el-option label="样式一" value="laser1" />
                <el-option label="样式二" value="laser2" />
                <el-option label="样式三" value="laser3" />
                <el-option label="样式四" value="laser4" />
              </el-select>
            </el-form-item>
            <el-form-item label="    ">
              <el-row :gutter="gutter">
                <el-col :span="12">
                  <el-switch v-model="form.data.radius" active-text="圆角"/>
                </el-col>
                <el-col :span="12">
                  <el-switch v-model="form.data.twentieth" active-text="20th"/>
                </el-col>
              </el-row>
            </el-form-item>
            <el-form-item label="缩放">
              <el-slider
                v-model="form.data.scale"
                :min="0.1"
                :max="1"
                :step="0.1"
             />
            </el-form-item>
          </div>

          <div v-if="form.card === 'rush-duel' && form.ui === true">
            <el-form-item label="语言">
              <el-select v-model="form.data.language" placeholder="请选择语言" @change="changeLanguageRushDuel">
                <el-option label="简体中文" value="sc" />
                <el-option label="日文" value="jp" />
              </el-select>
            </el-form-item>
            <el-form-item label="卡名">
              <el-input v-model="form.data.name" placeholder="请输入卡名" />
            </el-form-item>
            <el-form-item label="颜色">
              <el-color-picker v-model="form.data.color" />
              <span class="tip">（自动选择清空）</span>
            </el-form-item>
            <el-form-item label="类型">
              <el-radio-group v-model="form.data.type">
                <el-radio-button label="monster">怪兽</el-radio-button>
                <el-radio-button label="spell">魔法</el-radio-button>
                <el-radio-button label="trap">陷阱</el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="属性">
              <el-radio-group v-model="form.data.attribute" class="attribute-radio-group">
                <el-radio-button label="dark">暗</el-radio-button>
                <el-radio-button label="light">光</el-radio-button>
                <el-radio-button label="earth">地</el-radio-button>
                <el-radio-button label="water">水</el-radio-button>
                <el-radio-button label="fire">炎</el-radio-button>
                <el-radio-button label="wind">风</el-radio-button>
                <el-radio-button label="divine">神</el-radio-button>
                <el-radio-button label="">无</el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="['spell','trap'].includes(form.data.type)" label="图标">
              <el-select v-model="form.data.icon" placeholder="请选择图标" clearable>
                <el-option label="装备" value="equip" />
                <el-option label="场地" value="filed" />
                <el-option label="速攻" value="quick-play" />
                <el-option label="仪式" value="ritual" />
                <el-option label="永续" value="continuous" />
                <el-option label="反击" value="counter" />
              </el-select>
            </el-form-item>
            <el-form-item label="图片">
              <el-space :size="10">
                <el-upload
                  action="/"
                  :show-file-list="false"
                  accept="image/*"
                  :before-upload="beforeUploadCard"
                >
                  <el-button type="primary">选择图片</el-button>
                </el-upload>
                <el-button plain @click="deleteImage">删除</el-button>
              </el-space>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="卡类">
              <el-select v-model="form.data.cardType" placeholder="请选择卡类">
                <el-option label="通常" value="normal" />
                <el-option label="效果" value="effect" />
                <el-option label="仪式" value="ritual" />
                <el-option label="融合" value="fusion" />
              </el-select>
            </el-form-item>
            <el-form-item v-if="(form.data.type === 'monster' && ['normal', 'effect', 'ritual', 'fusion', 'synchro', 'token'].includes(form.data.cardType)) || (form.data.type === 'pendulum' && ['normal-pendulum', 'effect-pendulum', 'ritual-pendulum', 'fusion-pendulum', 'synchro-pendulum'].includes(form.data.pendulumType))" label="星级">
              <el-input-number
                v-model="form.data.level"
                :min="0"
                :max="13"
                :precision="0"
              />
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="种族">
              <el-input v-model="form.data.monsterType" placeholder="请输入种族" />
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="MAX">
              <el-input-number
                v-model="form.data.maximumAtk"
                :min="0"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（MAXIMUM ATK）</span>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="ATK">
              <el-input-number
                v-model="form.data.atk"
                :min="-1"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（?：-1）</span>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="DEF">
              <el-input-number
                v-model="form.data.def"
                :min="-1"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（?：-1）</span>
            </el-form-item>
            <el-form-item label="效果">
              <el-row :gutter="gutter">
                <el-col :span="12">
                  <el-switch v-model="form.data.firstLineCompress" active-text="首行压缩" />
                </el-col>
                <el-col :span="12">
                  <el-switch v-model="form.data.descriptionAlign" active-text="文本居中" />
                </el-col>
              </el-row>
              <el-input
                v-model="form.data.description"
                style="margin-top: 10px"
                type="textarea"
                :autosize="{minRows: 3}"
                placeholder="请输入效果"
              />
            </el-form-item>
            <el-form-item label="字号">
              <el-slider
                v-model="form.data.descriptionZoom"
                :min="0.5"
                :max="1.5"
                :step="0.02"
                :marks="descriptionZoomMarks"
              />
            </el-form-item>
            <el-form-item label="卡包">
              <el-input v-model="form.data.package" placeholder="请输入卡包" />
            </el-form-item>
            <el-form-item label="密码">
              <el-input v-model="form.data.password" placeholder="请输入密码" />
            </el-form-item>
            <el-form-item label="罕贵">
              <el-select
                v-model="form.data.rare"
                placeholder="请选择罕贵"
                clearable
              >
                <el-option label="SR" value="sr" />
                <el-option label="RR" value="rr" />
                <el-option label="PSER" value="pser" />
              </el-select>
            </el-form-item>
            <el-form-item label="角标">
              <el-select
                v-model="form.data.laser"
                placeholder="请选择角标"
                clearable
              >
                <el-option label="样式一" value="laser1" />
                <el-option label="样式二" value="laser2" />
                <el-option label="样式三" value="laser3" />
                <el-option label="样式四" value="laser4" />
              </el-select>
            </el-form-item>
            <el-form-item label="    ">
              <el-row :gutter="gutter">
                <el-col :span="12">
                  <el-switch v-model="form.data.radius" active-text="圆角"/>
                </el-col>
                <el-col :span="12">
                  <el-switch v-model="form.data.legend" active-text="传说"/>
                </el-col>
              </el-row>
            </el-form-item>
            <el-form-item label="缩放">
              <el-slider
                v-model="form.data.scale"
                :min="0.1"
                :max="1"
                :step="0.1"
              />
            </el-form-item>
          </div>

          <div v-if="form.card === 'yugioh-back' && form.ui === true">
            <el-form-item label="类型">
              <el-select
                v-model="form.data.type"
                placeholder="请选择类型"
              >
                <el-option label="通常" value="normal" />
                <el-option label="巨神兵" value="tormentor" />
                <el-option label="天空龙" value="sky-dragon" />
                <el-option label="翼神龙" value="winged-dragon" />
              </el-select>
            </el-form-item>
            <el-form-item label="标志">
              <el-select
                v-model="form.data.logo"
                placeholder="请选择标志"
                clearable
              >
                <el-option label="OCG" value="ocg" />
                <el-option label="TCG" value="tcg" />
                <el-option label="RD" value="rd" />
              </el-select>
            </el-form-item>
            <el-row :gutter="gutter">
              <el-col :span="8">
                <el-form-item label="圆角">
                  <el-switch v-model="form.data.radius" />
                </el-form-item>
              </el-col>
              <el-col :span="8">
                <el-form-item label="K标">
                  <el-switch v-model="form.data.konami" />
                </el-form-item>
              </el-col>
              <el-col :span="8">
                <el-form-item label="R标">
                  <el-switch v-model="form.data.register" />
                </el-form-item>
              </el-col>
            </el-row>
            <el-form-item label="缩放">
              <el-slider
                v-model="form.data.scale"
                :min="0.1"
                :max="1"
                :step="0.1"
              />
            </el-form-item>
          </div>

          <div v-if="form.card === 'field-center' && form.ui === true">
            <el-form-item label="图片">
              <el-space :size="10">
                <el-upload
                  action="/"
                  :show-file-list="false"
                  accept="image/*"
                  :before-upload="beforeUploadCenter"
                >
                  <el-button type="primary">选择图片</el-button>
                </el-upload>
                <el-button plain @click="deleteImage">删除</el-button>
              </el-space>
            </el-form-item>
            <el-row :gutter="gutter">
              <el-col :span="12">
                <el-form-item label="圆角">
                  <el-switch v-model="form.data.radius" />
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="卡背">
                  <el-switch v-model="form.data.cardBack" />
                </el-form-item>
              </el-col>
            </el-row>
            <el-form-item label="缩放">
              <el-slider
                v-model="form.data.scale"
                :min="0.1"
                :max="1"
                :step="0.1"
              />
            </el-form-item>
          </div>

          <div v-if="form.card === 'yugioh-series-2' && form.ui === true">
            <el-form-item label="卡名">
              <el-input v-model="form.data.name" placeholder="请输入卡名" />
            </el-form-item>
            <el-form-item label="对齐">
              <el-radio-group v-model="form.data.align">
                <el-radio-button label="left">
                  <i class="fa-solid fa-align-left" />
                </el-radio-button>
                <el-radio-button label="center">
                  <i class="fa-solid fa-align-center" />
                </el-radio-button>
                <el-radio-button label="right">
                  <i class="fa-solid fa-align-right" />
                </el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item label="类型">
              <el-radio-group v-model="form.data.type">
               <el-radio-button label="monster">怪兽</el-radio-button>
                <el-radio-button label="spell">魔法</el-radio-button>
                <el-radio-button label="trap">陷阱</el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="属性">
              <el-radio-group v-model="form.data.attribute" class="attribute-radio-group">
                <el-radio-button label="dark">暗</el-radio-button>
                <el-radio-button label="light">光</el-radio-button>
                <el-radio-button label="earth">地</el-radio-button>
                <el-radio-button label="water">水</el-radio-button>
                <el-radio-button label="fire">炎</el-radio-button>
                <el-radio-button label="wind">风</el-radio-button>
                <el-radio-button label="divine">神</el-radio-button>
                <el-radio-button label="">无</el-radio-button>
              </el-radio-group>
            </el-form-item>
            <el-form-item v-if="['spell','trap'].includes(form.data.type)" label="图标">
              <el-select v-model="form.data.icon" placeholder="请选择图标" clearable>
                <el-option label="装备" value="equip" />
                <el-option label="场地" value="filed" />
                <el-option label="速攻" value="quick-play" />
                <el-option label="仪式" value="ritual" />
                <el-option label="永续" value="continuous" />
                <el-option label="反击" value="counter" />
              </el-select>
            </el-form-item>
            <el-form-item label="图片">
              <el-space :size="10">
                <el-upload
                  action="/"
                  :show-file-list="false"
                  accept="image/*"
                  :before-upload="beforeUploadCard"
                >
                  <el-button type="primary">选择图片</el-button>
                </el-upload>
                <el-button plain @click="deleteImage">删除</el-button>
              </el-space>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="卡类">
              <el-select v-model="form.data.cardType" placeholder="请选择卡类">
                <el-option label="通常" value="normal" />
                <el-option label="效果" value="effect" />
                <el-option label="仪式" value="ritual" />
                <el-option label="融合" value="fusion" />
                <el-option label="巨神兵" value="tormentor" />
                <el-option label="天空龙" value="sky-dragon" />
                <el-option label="翼神龙" value="winged-dragon" />
              </el-select>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="星级">
              <el-input-number
                v-model="form.data.level"
                :min="0"
                :max="12"
                :precision="0"
              />
            </el-form-item>
            <el-form-item v-if="['monster','pendulum'].includes(form.data.type)" label="种族">
              <el-input v-model="form.data.monsterType" placeholder="请输入种族" />
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="ATK">
              <el-input-number
                v-model="form.data.atk"
                :min="-2"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（?：-1，∞：-2）</span>
            </el-form-item>
            <el-form-item v-if="form.data.type === 'monster'" label="DEF">
              <el-input-number
              v-model="form.data.def"
                :min="-2"
                :max="9999"
                :precision="0"
              />
              <span class="tip">（?：-1，∞：-2）</span>
            </el-form-item>
            <el-form-item label="效果">
              <el-row :gutter="gutter">
                <el-col :span="12">
                  <el-switch v-model="form.data.firstLineCompress" active-text="首行压缩" />
                </el-col>
                <el-col :span="12">
                  <el-switch v-model="form.data.descriptionAlign" active-text="文本居中" />
                </el-col>
              </el-row>
              <el-input
                v-model="form.data.description"
                style="margin-top: 10px"
                type="textarea"
                :autosize="{minRows: 3}"
                placeholder="请输入效果"
              />
            </el-form-item>
            <el-form-item label="字号">
              <el-slider
                v-model="form.data.descriptionZoom"
                :min="0.5"
                :max="1.5"
                :step="0.02"
                :marks="descriptionZoomMarks"
              />
            </el-form-item>
            <el-form-item label="卡包">
              <el-input v-model="form.data.package" placeholder="请输入卡包" />
            </el-form-item>
            <el-form-item label="密码">
                <el-input v-model="form.data.password" placeholder="请输入密码" />
            </el-form-item>
            <el-form-item label="版权">
              <el-select
                v-model="form.data.copyright"
                placeholder="请选择版权"
                clearable
                fit-input-width
              >
                <el-option label="©スタジオ・ダイス/集英社・テレビ東京・KONAMI" value="jp" />
              </el-select>
            </el-form-item>
            <el-form-item label="角标">
              <el-select
                v-model="form.data.laser"
                placeholder="请选择角标"
                clearable
              >
                <el-option label="样式一" value="laser1" />
                <el-option label="样式二" value="laser2" />
                <el-option label="样式三" value="laser3" />
                <el-option label="样式四" value="laser4" />
              </el-select>
            </el-form-item>
            <el-row :gutter="gutter">
              <el-col :span="12">
                <el-form-item label="圆角">
                  <el-switch v-model="form.data.radius" />
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="20th">
                  <el-switch v-model="form.data.twentieth" />
                </el-form-item>
              </el-col>
            </el-row>
            <el-form-item label="缩放">
              <el-slider
                v-model="form.data.scale"
                :min="0.1"
                :max="1"
                :step="0.1"
             />
            </el-form-item>
          </div>

          <el-form-item v-if="form.ui === false" label="数据">
            <json-editor-vue
              v-model="jsonData"
              style="width: 100%"
              mode="text"
              v-bind="jsonOption"
            />
          </el-form-item>
        </el-form>

        <div class="button-group">
          <el-row :gutter="gutter">
              <el-col :span="12">
                <el-upload
                  action="/"
                  :show-file-list="false"
                  accept="application/json"
                  :before-upload="importJson"
                >
                  <el-button plain>导入数据</el-button>
                </el-upload>
              </el-col>
              <el-col :span="12">
                <el-button plain @click="exportJson">导出数据</el-button>
              </el-col>
            <el-button type="primary" @click="exportImage">导出图片</el-button>
          </el-row>
        </div>

        <el-dialog
          v-model="CropperDialog.show"
          title="图片裁切"
          :width="800"
          :close-on-click-modal="false"
          :close-on-press-escape="false"
          :show-close="false"
        >
          <div style="height: 600px;">
            <vue-cropper
              ref="cropper"
              :img="CropperDialog.image"
              :can-scale="false"
              :auto-crop="true"
              :auto-crop-width="9999"
              :auto-crop-height="9999"
              :fixed="true"
              :fixed-number="CropperDialog.fixedNumber"
              :can-move-box="true"
              :center-box="true"
            />
          </div>
          <template #footer>
            <el-button @click="CropperDialog.show = false">取消</el-button>
            <el-button type="primary" @click="confirmCrop">确定</el-button>
          </template>
        </el-dialog>

      </div>
    </div>
  </div>
</template>

<script setup>
import { Icon } from '@iconify/vue';
import { onBeforeUnmount, onMounted, reactive, ref, shallowRef, watch } from 'vue';
import { FieldCenterCard, RushDuelCard, YugiohBackCard, YugiohCard, YugiohSeries2Card } from 'yugioh-card';
import JsonEditorVue from 'json-editor-vue';
import { ElMessage } from 'element-plus';
import { VueCropper }  from "vue-cropper";
import 'vue-cropper/dist/index.css';
import loadImage from 'blueimp-load-image';
import { computed } from 'vue'
import fieldCenter from '@/assets/default/field-center';
import rushDuel from '@/assets/default/rush-duel';
import rushDuel_sc from '@/assets/default/rush-duel/sc';
import rushDuel_jp from '@/assets/default/rush-duel/jp';
import yugiohBack from '@/assets/default/yugioh-back';
import yugioh from '@/assets/default/yugioh';
import yugioh_sc from '@/assets/default/yugioh/sc';
import yugioh_tc from '@/assets/default/yugioh/tc';
import yugioh_jp from '@/assets/default/yugioh/jp';
import yugioh_kr from '@/assets/default/yugioh/kr';
import yugioh_en from '@/assets/default/yugioh/en';
import yugioh_astral from '@/assets/default/yugioh/astral';
import yugiohSeries2 from '@/assets/default/yugioh-series-2';

const card = ref(null);
const cardLeaf = shallowRef(null);
const cropper = ref(null);
const form = reactive({
  card: 'yugioh',
  data: {},
  ui: true,
});
const CropperDialog = reactive({
  image: '',
  fixedNumber: [1,1],
  show: false,
});
const jsonData = ref('');
const jsonOption = reactive({
  mainMenuBar: false,
  statusBar: false,
});

onMounted(() => {
  changeCard();
});

onBeforeUnmount(() => {
  cardLeaf.value?.leafer.destroy();
});

const changeCard = () => {
  cardLeaf.value?.leafer.destroy();
  let Card;
  switch (form.card) {
    case 'yugioh':
      form.data = yugioh;
      Card = YugiohCard;
      break;
    case 'rush-duel':
      form.data = rushDuel;
      Card = RushDuelCard;
      break;
    case 'yugioh-back':
      form.data = yugiohBack;
      Card = YugiohBackCard;
      break;
    case 'field-center':
      form.data = fieldCenter;
      Card = FieldCenterCard;
      break;
    case 'yugioh-series-2':
      form.data = yugiohSeries2;
      Card = YugiohSeries2Card;
      break;
    default:
      form.data = yugioh;
      Card = YugiohCard;
  }
  cardLeaf.value = new Card({
    view: card.value,
    data: form.data,
    resourcePath: process.env.NODE_ENV === 'production' ? 'https://raw.githubusercontent.com/kooriookami/yugioh-card/refs/heads/master/src/assets/yugioh-card' : 'src/assets/yugioh-card',
  });
  jsonData.value = form.data;
};
const changeLanguageYugioh = (value) => {
  if (value === 'sc') {
    Object.assign(form.data, yugioh_sc);
  } else if (value === 'tc') {
    Object.assign(form.data, yugioh_tc);
  } else if (value === 'jp') {
    Object.assign(form.data, yugioh_jp);
  } else if (value === 'kr') {
    Object.assign(form.data, yugioh_kr);
  } else if (value === 'en') {
    Object.assign(form.data, yugioh_en);
  } else if (value === 'astral') {
    Object.assign(form.data, yugioh_astral);
  }
};
const changeLanguageRushDuel = (value) => {
  if (value === 'sc') {
    Object.assign(form.data, rushDuel_sc);
  } else if (value === 'jp') {
    Object.assign(form.data, rushDuel_jp);
  }
};
const changeLanguageYugiohSeries2 = (value) => {
  if (value === 'sc') {
    Object.assign(form.data, yugiohSeries2_sc);
  } else if (value === 'jp') {
    Object.assign(form.data, yugiohSeries2_jp);
  }
};
const toggleArrow = (item) => {
  if (form.data.arrowList.includes(item)) {
    form.data.arrowList = form.data.arrowList.filter(value => value !== item);
  } else {
    form.data.arrowList.push(item);
  }
};
const arrowItemStyle = (item) => {
  let border = '';
  let color = '';
  if (form.data.arrowList.includes(item)) {
    border = '1px solid darkorange';
    color = 'darkorange';
  }
  return {
    border: border,
    color: color,
    visibility: item === 9 ? 'hidden' : '',
  };
};
const beforeUploadCard = (file) => {
  const isImage = file.type.includes('image');
  if (isImage) {
    loadImage(file, {
      canvas: true,
      orientation: true
    }).then(data => {
      CropperDialog.image = data.image.toDataURL('image/png');
      CropperDialog.fixedNumber = [1,1];
      CropperDialog.show = true;
    });
  } else {
    ElMessage.error('请选择正确图片格式');
  }
  return false;
};
const beforeUploadCenter = (file) => {
  const isImage = file.type.includes('image');
  if (isImage) {
    loadImage(file, {
      canvas: true,
      orientation: true
    }).then(data => {
      CropperDialog.image = data.image.toDataURL('image/png');
      CropperDialog.fixedNumber = [1308,1907];
      CropperDialog.show = true;
    });
  } else {
    ElMessage.error('请选择正确图片格式');
  }
  return false;
};
const confirmCrop = () => {
  if (!cropper.value) return;
  cropper.value.getCropData((data) => {
    form.data.image = data; 
    CropperDialog.show = false;
  });
};
const setImage = image => {
  form.data.image = image;
};
const deleteImage = () => {
  form.data.image = '';
};
const importJson = (file) => {
  const reader = new FileReader();
  reader.onload = (e) => {
    try {
      const json = JSON.parse(e.target.result);
      Object.assign(form.data, json);
    } catch (err) {
      console.error('JSON 解析失敗:', err);
      ElMessage.error('JSON 格式錯誤');
    }
  };
  reader.readAsText(file);
  return false;
};
const exportJson = () => {
  const data = JSON.stringify(form.data);
  const blob = new Blob([data], { type: 'application/json' });
  const url = window.URL.createObjectURL(blob);
  const link = document.createElement('a');
  link.href = url;

  link.download = `${cardName.value}.json`;
  
  link.click();
  window.URL.revokeObjectURL(url);
};
const exportImage = () => {
  cardLeaf.value.leafer.export(`${cardName.value}.png`, {
    screenshot: true,
    pixelRatio: devicePixelRatio,
  });
};
const cardName = computed(() => {
  if (!form.data.name) return '未命名卡片';
  return form.data.name.replace(/\[(.*?)\(.*?\)]/g, '$1');
});

watch(() => form.data, () => {
  try {
    cardLeaf.value.setData(form.data);
  } catch (e) {

  }
},{deep:true});

watch(() => jsonData.value, () => {
  try {
    form.data = JSON.parse(jsonData.value);
    cardLeaf.value.setData(form.data);
  } catch (e) {

  }
});

const toGithub = () => {
  open('https://github.com/kooriookami/yugioh-card');
};
</script>

<style lang="scss" scoped>
.yugioh-card-container {
  height: 100vh;
  display: flex;
  overflow: hidden;

  .yugioh-card {
    height: 100%;
    overflow: auto;
    flex-grow: 1;
    position: relative;
    padding: 20px;

    .card {
      display: inline-block;
      vertical-align: top;
    }
  }

  .form {
    height: 100%;
    overflow: auto;
    width: 600px;
    flex-shrink: 0;
    border-left: 1px solid var(--border-color);

    .form-header {
      padding: 30px 20px;
      font-size: 18px;
      font-weight: bold;
      border-bottom: 1px solid var(--border-color);

      .form-title {
        display: flex;
        flex-wrap: wrap;
        align-items: center;

        .github-icon {
          margin-left: 5px;
          cursor: pointer;
        }
      }

      .form-description {
        margin-top: 20px;
        font-size: 12px;
        font-weight: normal;
        color: var(--info-color);
      }
    }

    .form-main {
      padding: 20px;

      .arrow-form {
        width: 130px;
        display: flex;
        flex-wrap: wrap;
        margin-right: -10px;
        margin-bottom: -10px;

        .arrow-item {
          width: 32px;
          height: 32px;
          margin-right: 10px;
          margin-bottom: 10px;
          border: 1px solid var(--border-color);
          border-radius: 4px;
          display: flex;
          justify-content: center;
          align-items: center;
          cursor: pointer;
          color: var(--placeholder-color);
          font-size: 18px;
        }
      }

      .attribute-radio-group {
        ::v-deep(.el-radio-button__inner) {
          padding: 8px 11px;
        }
      }

      .button-group {
        margin-top: 20px;

        ::v-deep(.el-upload) {
              width: 100%;
            }

        .el-button {
          width: 100%;
        }
      }
    }
  }
}
</style>
