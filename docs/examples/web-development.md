# Web 开发实战案例

真实的 Web 开发场景中使用 Claude Code 的案例。

## 案例 1: 实现购物车功能

### 场景描述

为一个 Next.js 电商项目添加完整的购物车功能。

### 初始请求

```
实现购物车功能，要求：

功能需求：
- 添加商品到购物车
- 修改商品数量
- 删除商品
- 计算总价
- 本地持久化（localStorage）

技术栈：
- Next.js 14 (App Router)
- TypeScript
- Zustand (状态管理)
- Tailwind CSS

请创建 todo list 跟踪进度。
```

### Claude Code 的执行过程

#### 1. 创建任务列表

Claude Code 创建了以下任务：

- [x] 创建购物车数据类型定义
- [x] 实现 Zustand store
- [x] 创建购物车组件
- [x] 实现添加/删除/更新功能
- [x] 添加本地持久化
- [x] 创建单元测试
- [x] 集成到现有页面

#### 2. 类型定义

创建 `types/cart.ts`：

```typescript
export interface CartItem {
  id: string;
  name: string;
  price: number;
  quantity: number;
  image?: string;
}

export interface CartState {
  items: CartItem[];
  addItem: (item: Omit<CartItem, 'quantity'>) => void;
  removeItem: (id: string) => void;
  updateQuantity: (id: string, quantity: number) => void;
  clearCart: () => void;
  total: number;
}
```

#### 3. Zustand Store

创建 `store/cartStore.ts`：

```typescript
import { create } from 'zustand';
import { persist } from 'zustand/middleware';
import { CartState, CartItem } from '@/types/cart';

export const useCartStore = create<CartState>()(
  persist(
    (set, get) => ({
      items: [],

      addItem: (item) => set((state) => {
        const existingItem = state.items.find((i) => i.id === item.id);

        if (existingItem) {
          return {
            items: state.items.map((i) =>
              i.id === item.id
                ? { ...i, quantity: i.quantity + 1 }
                : i
            ),
          };
        }

        return {
          items: [...state.items, { ...item, quantity: 1 }],
        };
      }),

      removeItem: (id) => set((state) => ({
        items: state.items.filter((item) => item.id !== id),
      })),

      updateQuantity: (id, quantity) => set((state) => ({
        items: state.items.map((item) =>
          item.id === id ? { ...item, quantity } : item
        ),
      })),

      clearCart: () => set({ items: [] }),

      get total() {
        return get().items.reduce(
          (sum, item) => sum + item.price * item.quantity,
          0
        );
      },
    }),
    {
      name: 'cart-storage',
    }
  )
);
```

#### 4. 购物车组件

创建 `components/Cart.tsx`：

```typescript
'use client';

import { useCartStore } from '@/store/cartStore';
import Image from 'next/image';

export function Cart() {
  const { items, removeItem, updateQuantity, total } = useCartStore();

  if (items.length === 0) {
    return (
      <div className="text-center py-12">
        <p className="text-gray-500">购物车是空的</p>
      </div>
    );
  }

  return (
    <div className="space-y-4">
      {items.map((item) => (
        <div key={item.id} className="flex items-center gap-4 p-4 border rounded-lg">
          {item.image && (
            <Image
              src={item.image}
              alt={item.name}
              width={80}
              height={80}
              className="rounded"
            />
          )}

          <div className="flex-1">
            <h3 className="font-semibold">{item.name}</h3>
            <p className="text-gray-600">¥{item.price}</p>
          </div>

          <div className="flex items-center gap-2">
            <button
              onClick={() => updateQuantity(item.id, Math.max(1, item.quantity - 1))}
              className="px-3 py-1 border rounded"
            >
              -
            </button>
            <span className="w-12 text-center">{item.quantity}</span>
            <button
              onClick={() => updateQuantity(item.id, item.quantity + 1)}
              className="px-3 py-1 border rounded"
            >
              +
            </button>
          </div>

          <button
            onClick={() => removeItem(item.id)}
            className="text-red-500 hover:text-red-700"
          >
            删除
          </button>
        </div>
      ))}

      <div className="border-t pt-4 text-right">
        <p className="text-xl font-bold">总计: ¥{total.toFixed(2)}</p>
      </div>
    </div>
  );
}
```

#### 5. 测试

创建 `__tests__/cartStore.test.ts`：

```typescript
import { renderHook, act } from '@testing-library/react';
import { useCartStore } from '@/store/cartStore';

describe('Cart Store', () => {
  beforeEach(() => {
    const { result } = renderHook(() => useCartStore());
    act(() => {
      result.current.clearCart();
    });
  });

  it('should add item to cart', () => {
    const { result } = renderHook(() => useCartStore());

    act(() => {
      result.current.addItem({
        id: '1',
        name: 'Test Product',
        price: 99.99,
      });
    });

    expect(result.current.items).toHaveLength(1);
    expect(result.current.items[0].quantity).toBe(1);
  });

  it('should increase quantity for existing item', () => {
    const { result } = renderHook(() => useCartStore());

    act(() => {
      result.current.addItem({
        id: '1',
        name: 'Test Product',
        price: 99.99,
      });
      result.current.addItem({
        id: '1',
        name: 'Test Product',
        price: 99.99,
      });
    });

    expect(result.current.items).toHaveLength(1);
    expect(result.current.items[0].quantity).toBe(2);
  });

  it('should calculate total correctly', () => {
    const { result } = renderHook(() => useCartStore());

    act(() => {
      result.current.addItem({
        id: '1',
        name: 'Product 1',
        price: 100,
      });
      result.current.addItem({
        id: '2',
        name: 'Product 2',
        price: 50,
      });
    });

    expect(result.current.total).toBe(150);
  });
});
```

### 关键学习点

1. **任务分解**: Claude Code 自动将复杂功能分解为可管理的步骤
2. **类型安全**: 使用 TypeScript 确保类型正确
3. **状态管理**: 使用 Zustand 实现简洁的状态管理
4. **持久化**: 使用 persist 中间件实现本地存储
5. **测试**: 完整的单元测试覆盖

### 后续优化建议

```
"购物车功能已完成，现在请：

1. 添加购物车图标和商品数量徽章到导航栏
2. 实现购物车侧边抽屉（点击图标打开）
3. 添加结账按钮和流程
4. 优化移动端显示"
```

## 案例 2: 性能优化

### 场景描述

产品列表页面加载缓慢，需要进行性能优化。

### 问题诊断请求

```
产品列表页面 (app/products/page.tsx) 加载很慢，帮我分析和优化：

当前问题：
- 首次加载需要 5 秒
- 滚动时卡顿
- 图片加载慢

请先诊断问题，然后提供优化方案。
```

### Claude Code 分析过程

#### 1. 代码审查

Claude Code 读取代码后发现的问题：

```typescript
// 问题代码示例
export default function ProductsPage() {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    // ❌ 问题1: 每次渲染都会创建新的函数
    fetch('/api/products')
      .then(res => res.json())
      .then(setProducts);
  }, []); // ❌ 问题2: 缺少依赖

  return (
    <div>
      {products.map(product => (
        // ❌ 问题3: 没有使用 Next.js Image 组件
        <img src={product.image} alt={product.name} />
      ))}
    </div>
  );
}
```

#### 2. 优化方案

Claude Code 提出的优化：

```typescript
'use client';

import { useState } from 'react';
import Image from 'next/image';
import { useInView } from 'react-intersection-observer';

interface Product {
  id: string;
  name: string;
  price: number;
  image: string;
}

// ✅ 优化1: 虚拟滚动 - 仅渲染可见项
function ProductCard({ product }: { product: Product }) {
  const { ref, inView } = useInView({
    triggerOnce: true,
    threshold: 0.1,
  });

  return (
    <div ref={ref} className="product-card">
      {inView && (
        <>
          {/* ✅ 优化2: 使用 Next.js Image 组件 */}
          <Image
            src={product.image}
            alt={product.name}
            width={300}
            height={300}
            loading="lazy"
            placeholder="blur"
            blurDataURL="/placeholder.jpg"
          />
          <h3>{product.name}</h3>
          <p>¥{product.price}</p>
        </>
      )}
    </div>
  );
}

// ✅ 优化3: 使用 SWR 进行数据获取和缓存
import useSWR from 'swr';

const fetcher = (url: string) => fetch(url).then(r => r.json());

export default function ProductsPage() {
  const { data: products, error, isLoading } = useSWR<Product[]>(
    '/api/products',
    fetcher,
    {
      revalidateOnFocus: false,
      dedupingInterval: 60000, // 1 分钟内去重
    }
  );

  if (isLoading) return <ProductSkeleton />;
  if (error) return <ErrorMessage />;

  return (
    <div className="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-4">
      {products?.map((product) => (
        <ProductCard key={product.id} product={product} />
      ))}
    </div>
  );
}
```

#### 3. 性能测试

```
"运行 Lighthouse 测试对比优化前后的性能"
```

结果：
- 首次加载: 5s → 1.2s
- Lighthouse 性能分数: 45 → 92
- 图片加载: 立即显示占位符，渐进加载

### 关键技术点

1. **Image 优化**: Next.js Image 组件自动优化
2. **懒加载**: Intersection Observer 实现按需加载
3. **数据缓存**: SWR 提供智能缓存和重新验证
4. **虚拟化**: 仅渲染可见内容

## 案例 3: Bug 修复

### 问题报告

```
用户报告登录后刷新页面会退出登录。

环境：
- Next.js 14 (App Router)
- 使用 NextAuth.js

错误信息：
- 控制台显示 "Session expired"
- 但用户刚登录不到 1 分钟

请帮我定位并修复这个问题。
```

### Claude Code 调试过程

#### 1. 检查配置

```typescript
// app/api/auth/[...nextauth]/route.ts

// ❌ 发现问题: session 策略设置错误
export const authOptions: NextAuthOptions = {
  session: {
    strategy: 'jwt',
    maxAge: 60, // ❌ 问题: 设置为 60 秒而不是 30 天
  },
  // ...
};
```

#### 2. 修复

```typescript
export const authOptions: NextAuthOptions = {
  session: {
    strategy: 'jwt',
    maxAge: 30 * 24 * 60 * 60, // ✅ 30 天
  },
  callbacks: {
    async jwt({ token, user }) {
      if (user) {
        token.id = user.id;
      }
      return token;
    },
    async session({ session, token }) {
      if (session.user) {
        session.user.id = token.id as string;
      }
      return session;
    },
  },
};
```

#### 3. 添加测试

```typescript
describe('Auth Session', () => {
  it('should persist session for 30 days', async () => {
    const session = await getServerSession(authOptions);
    expect(session).toBeDefined();

    // 模拟 29 天后
    const futureDate = new Date();
    futureDate.setDate(futureDate.getDate() + 29);

    // Session 应该仍然有效
    // ... 测试逻辑
  });
});
```

### 经验总结

1. **配置检查**: 首先检查配置文件
2. **日志分析**: 查看控制台和服务器日志
3. **测试覆盖**: 添加测试防止回归
4. **文档记录**: 在代码中添加注释说明

## 更多案例

查看其他案例：
- [数据分析](./data-analysis.md)
- [自动化脚本](./automation.md)
- [多语言项目](./multilingual.md)
