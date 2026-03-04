---
title: 【H5】React使用D3js实现关系图
date: 2026-01-08 11:33:25
categories: [React]
tags: [React, D3js, H5]
---

文档：[https://d3js.org/](https://d3js.org/)

## 背景

项目是 Taro+React 的 H5 应用，需要实现一个人际关系图，节点样式比较复杂，需要自定义节点。

## 安装

```bash
npm install d3
```

## 实现

```jsx
import React, { useEffect, useRef, useState } from 'react'
import { View, Text } from '@tarojs/components'
import Taro from '@tarojs/taro'
import { useCDN } from '@/shared/hooks'
import * as d3 from 'd3'

// 定义节点类型接口
export interface D3GraphNode {
  id: string
  size: number
  data: {
    name: string
    avatar: string
    isCenter?: boolean
  }
}

// 定义边类型接口
export interface D3GraphEdge {
  source: string
  target: string
}

const D3Graph: React.FC<{
  centerNode?: any
  contactNode?: any[]
  defaultZoomRatio?: number
}> = (props) => {
  const containerRef = useRef<HTMLDivElement>(null)
  const svgRef = useRef<SVGSVGElement>(null)
  const graphRef = useRef<{
    simulation: d3.Simulation<d3.SimulationNodeDatum, undefined> | null
    zoom: d3.ZoomBehavior<SVGSVGElement, unknown> | null
  }>({ simulation: null, zoom: null })

  const sysInfo = Taro.getSystemInfoSync()
  const width = sysInfo.windowWidth
  const height = sysInfo.windowHeight

  // 处理节点点击事件
  const handleNodeClick = (nodeId: string) => {}

  // 包含中文标点和全角符号
  const containsChineseExtended = (str: string) => {
    return /[\u4E00-\u9FA5\u3000-\u303F\uFF00-\uFFEF]/.test(str);
  }

  // 准备数据
  useEffect(() => {
    if (!containerRef.current || process.env.TARO_ENV !== 'h5') return

    const nodes: any[] = []
    const edges: any[] = []

    if (props.centerNode) {
      nodes.push({
        id: props.centerNode?.id,
        size: 66,
        ...props.centerNode,
        isCenter: true,
        // 初始化中心节点位置为容器中心
        x: width / 2,
        y: height / 2 - 70
      })

      if (props.contactNode) {
        props.contactNode?.forEach((node, index) => {
          nodes.push({
            id: node?.id,
            size: node?.size || 46,
            ...node
          })

          // 根据索引设置不同的距离范围
          let minDistance, maxDistance;
          if (index < 50) {
            // 前50人：距离从30到100随机
            minDistance = 50;
            maxDistance = 100;
          } else {
            // 每50人增加距离范围
            const group = Math.floor(index / 50);
            minDistance = 50 + (group - 1) * 100;
            maxDistance = 100 + (group - 1) * 100;
          }

          edges.push({
            source: props.centerNode?.id,
            target: node?.id,
            distance: Math.random() * (maxDistance - minDistance) + minDistance
          })
        })
      }
    }

    // 清除旧的图形
    const container = containerRef.current
    container.innerHTML = ''

    // 创建SVG
    const svg = d3.select(container)
      .append('svg')
      .attr('width', '100%')
      .attr('height', '100%')
      .attr('viewBox', `0 0 ${width} ${height}`)
      .attr('preserveAspectRatio', 'xMidYMid meet')

    svgRef.current = svg.node() as SVGSVGElement

    // 创建缩放容器
    const g = svg.append('g')

    // 实现缩放功能
    const zoom = d3.zoom<SVGSVGElement, unknown>()
      .scaleExtent([0.5, 5])
      .on('zoom', (event) => {
        g.attr('transform', event.transform)
      })

    svg.call(zoom)
    // 设置默认缩放比，默认为0.8，并将图形中心对准容器中心
    const zoomRatio = props.defaultZoomRatio || 1
    // 计算平移量，使缩放后的图形中心与容器中心重合
    const translateX = (width - width * zoomRatio) / 2
    const translateY = (height - height * zoomRatio) / 2
    svg.call(zoom.transform, d3.zoomIdentity.translate(translateX, translateY).scale(zoomRatio))
    graphRef.current.zoom = zoom

    // 创建力导向图
    const simulation = d3.forceSimulation(nodes as any)
      .force('link', d3.forceLink(edges as any).id((d: any) => d.id).distance((d: any) => d.distance || 100).strength(0.5))
      .force('charge', d3.forceManyBody().strength(-500))
      .force('center', d3.forceCenter(width / 2, height / 2))
      .force('collision', d3.forceCollide().radius((d: any) => Math.max(d.size / 2, 23) + 10))

    graphRef.current.simulation = simulation

    // 创建边
    const link = g.append('g')
      .selectAll('line')
      .data(edges)
      .enter()
      .append('line')
      .attr('stroke', '#ccc')
      .attr('stroke-opacity', 0.6)
      .attr('stroke-width', 1)

    // 创建节点组
    const node = g.append('g')
      .selectAll('g')
      .data(nodes)
      .enter()
      .append('g')
      .call(d3.drag<SVGGElement, any>()
        .on('start', dragstarted)
        .on('drag', dragged)
        .on('end', dragended))

    // 1. 创建节点容器（使用g元素作为容器）
    const nodeContainer = node.append('g')

    // 2. 实现双层边框效果
    // 2.1 内层白色边框（放在最底层）
    nodeContainer.append('circle')
      .attr('r', (d: any) => d.size / 2)
      .attr('fill', 'none')
      .attr('stroke', '#fff')
      .attr('stroke-width', 2)

    // 3. 添加圆形背景（根据是否有头像和是否是中心节点决定颜色）
    nodeContainer.append('circle')
      .attr('r', (d: any) => d.size / 2)
      .attr('fill', (d: any) => {
        if (d.avatar) {
          // 如果有头像，保持白色背景
          return '#fff';
        } else {
          // 如果没有头像，根据是否是中心节点设置颜色
          return d.isCenter ? '#000000' : '#155DFC';
        }
      })

    // 4. 绘制节点头像 - 只有当有头像时才绘制
    nodeContainer.filter((d: any) => d.avatar).append('image')
      .attr('href', (d: any) => d.avatar)
      .attr('x', (d: any) => -d.size / 2)
      .attr('y', (d: any) => -d.size / 2)
      .attr('width', (d: any) => d.size)
      .attr('height', (d: any) => d.size)
      .attr('style', (d: any) => `clip-path: circle(${d.size / 2}px at center);`)

    // 5. 当没有头像时，在圆形背景中间显示name的第一个字
    nodeContainer.filter((d: any) => !d.avatar).append('text')
      .attr('text-anchor', 'middle')
      .attr('font-size', (d: any) => d.isCenter ? 20 : 16)
      .attr('fill', '#ffffff')
      .attr('font-weight', 'bold')
      .attr('alignment-baseline', 'top')
      .text((d: any) => {
        const name = d.name || '';
        return name.charAt(0) || '';
      })

    // 5. 添加半透明背景的名称标签（高度为头像的三分之一，圆弧朝下，完全在头像内）
    // 5.1 创建圆弧形的名称背景（使用路径path，放在头像之上）
    nodeContainer.filter((d: any) => d.name).append('path')
      .attr('d', (d: any) => {
        const radius = d.size / 2;
        const labelHeight = d.size / 3; // 高度设置为头像高度的三分之一
        const topY = radius - labelHeight;
        const bottomY = 3;

        // 调整圆弧半径，使其完全在头像内部
        const arcRadius = radius;

        // 绘制圆弧形路径：左上角 → 左下角 → 右下角（圆弧）→ 右上角 → 闭合
        // 确保路径完全在头像内部
        return `M${-radius} ${topY} L${-radius} ${bottomY} A${arcRadius} ${arcRadius} 0 0 0 ${radius} ${bottomY} L${radius} ${topY} Z`;
      })
      .attr('fill', 'rgba(255, 255, 255, 0.77)')

    // 5.2 名称文本 - 确保在标签中居中显示，超过4个字换行
    nodeContainer.filter((d: any) => d.name).append('text')
      .attr('text-anchor', 'middle')
      .attr('font-size', (d: any) => d.isCenter ? 7 : 5)
      .attr('fill', '#333')
      .attr('font-weight', 'normal')
      .attr('alignment-baseline', 'middle')
      .each((d: any, i, nodes) => {
        const textElement = d3.select(nodes[i]);
        const name = d.name || '';
        const radius = d.size / 2;
        const labelHeight = d.size / 3;
        const fontSize = d.isCenter ? 7 : 5;

        // 计算名称标签的垂直范围（基于节点中心的坐标系统）
        const labelTop = radius - labelHeight; // 标签顶部距离节点中心的距离
        const labelBottom = radius; // 标签底部距离节点中心的距离
        const labelMiddle = (labelTop + labelBottom) / 2; // 标签中间距离节点中心的距离

        // 设置文本元素的垂直位置为名称标签的中间
        textElement.attr('y', labelMiddle);

        // 名称最多显示6个字符，同时根据fontSize*4的规则换行
        const truncatedName = name.substring(0, 6); // 最多显示6个字符
        const maxFirstLineLength = 8
        let lineCount = 0
        let line1 = ''
        let line2 = ''
        for(const char of truncatedName) {
          if (lineCount < maxFirstLineLength) {
            if(containsChineseExtended(char)) {
              line1 += char
              lineCount += 2
            } else {
              line1 += char
              lineCount += 1
            }
          } else {
            line2 += char
          }
        }

        // 清空现有内容
        textElement.text('');

        // 添加第一行文本
        textElement.append('tspan')
          .attr('x', 0)
          .attr('dy', line2 == '' ? 0 : -fontSize / 2 + (d.isCenter ? 1 : 0))
          .text(line1);

        // 添加第二行文本
        if (line2) {
          if (name.length > 6) {
            line2 = line2 + '...'
          }
          textElement.append('tspan')
            .attr('x', 0)
            .attr('dy', fontSize)
            .text(line2);
        }
      })

    // 6. 外层彩色边框（放在最后绘制，确保显示在所有元素上层）
    nodeContainer.append('circle')
      .attr('r', (d: any) => d.size / 2)
      .attr('fill', 'none')
      .attr('stroke', (d: any) => d.isCenter ? '#000000' : '#155DFC')
      .attr('stroke-width', 3)
    nodeContainer.append('circle')
      .attr('r', (d: any) => d.size / 2 - 2)
      .attr('fill', 'none')
      .attr('stroke', '#FFFFFF')
      .attr('stroke-width', 2)

    // 添加点击事件
    node.on('click', (event, d: any) => {
      handleNodeClick(d.id)
    })

    // 力导向图更新函数
    simulation.on('tick', () => {
      link
        .attr('x1', (d: any) => d.source.x)
        .attr('y1', (d: any) => d.source.y)
        .attr('x2', (d: any) => d.target.x)
        .attr('y2', (d: any) => d.target.y)

      node
        .attr('transform', (d: any) => `translate(${d.x},${d.y})`)
    })

    // 拖拽事件处理函数
    function dragstarted(event: any, d: any) {
      if (!event.active) simulation.alphaTarget(0.3).restart()
      d.fx = d.x
      d.fy = d.y
    }

    function dragged(event: any, d: any) {
      d.fx = event.x
      d.fy = event.y
    }

    function dragended(event: any, d: any) {
      if (!event.active) simulation.alphaTarget(0)
      d.fx = null
      d.fy = null
    }

    // 清理函数
    return () => {
      if (graphRef.current.simulation) {
        graphRef.current.simulation.stop()
      }
      if (containerRef.current) {
        containerRef.current.innerHTML = ''
      }
    }
  }, [props.centerNode, props.contactNode])

  return (
    <View
      ref={containerRef}
      style={{
        width: '100%',
        height: '100%',
        position: 'relative',
        background: '#ffffff'
      }}
    />
  )
}

export default D3Graph

```
