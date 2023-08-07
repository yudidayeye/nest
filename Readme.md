<!--
 * @Description:jin
 * @Author: lijin
 * @Date: 2023-08-07 14:48:37
 * @LastEditTime: 2023-08-07 16:22:39
 * @LastEditors:
-->

# 源码调试 nest.js

## 步骤

- 源码下载：https://github.com/nestjs/nest

- 源码构建：修改 `tsconfig` 配置文件如下 -- build 生成 sourcemap 文件到 `/node_modules/@nestjs`
  ```json
  /package/tsconfig.build.json
  {
    "compilerOptions": {
      "composite": true,
      "module": "commonjs",
      "declaration": true,
      "noImplicitAny": false,
      "skipLibCheck": true,
      "noUnusedLocals": false,
      "importHelpers": true,
      "removeComments": false,
      "noLib": false,
      "emitDecoratorMetadata": true,
      "experimentalDecorators": true,
      "useUnknownInCatchVariables": false,
      "target": "ES2021",
      "sourceMap": true, // 新增-解决生成sourcemap文件问题
      "inlineSources": true, // 新增-解决生成sourcemap文件问题
      "allowJs": false,
      "strict": true,
      "strictNullChecks": false,
      "types": ["node"]
    }
  }
  ```
- 项目调试：添加调试配置如下 -- 断点 -- 调试运行项目
  ```json
  /.vscode/launch.json
  {
    "version": "0.2.0",
    "configurations": [
      {
        "name": "Launch via NPM",
        "request": "launch",
        "runtimeArgs": ["run-script", "start"],
        "runtimeExecutable": "npm",
        "skipFiles": ["<node_internals>/**"],
        "type": "node",
        // 修改console位置
        "console": "integratedTerminal",
        // 调试弄得项目需要修改此配置，默认配置不允许读取node_modules中的sourcemap文件，用于解决调用栈代码不是源码的问题
        "resolveSourceMapLocations": ["${workspaceFolder}/**"],
        // 设置当前工作目录
        "cwd": "${workspaceFolder}/sample/01-cats-app"
      }
    ]
  }
  ```
- 解决 ·sourcemap· 代码只读问题（sourcemap 路径不正确问题）：修改 `/package` 各个子目录的 `tsconfig.build.json` ，配置 `sourceRoot` 为源码根路径 -- 重新构建,调试

  ```json
  /packages/core/tsconfig.buld.jsons
  {
    "extends": "../tsconfig.build.json",
    "compilerOptions": {
      "outDir": ".",
      "rootDir": ".",
      "paths": {
        "@nestjs/common": ["../common"],
        "@nestjs/common/*": ["../common/*"],
        "@nestjs/microservices": ["../microservices"],
        "@nestjs/microservices/*": ["../microservices/*"],
        "@nestjs/websockets": ["../websockets"],
        "@nestjs/websockets/*": ["../websockets/*"]
      },
      // 配置 sourcemap 根路径，解决sourcemap路径错误问题
      "sourceRoot": "D:\\04-learn-font\\learn-node\\nest\\packages\\core"
    },
    "exclude": ["node_modules", "dist", "test/**/*", "*.spec.ts"],
    "references": [
      {
        "path": "../common/tsconfig.build.json"
      }
    ]
  }
  ```
