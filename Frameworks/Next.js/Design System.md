---
type: framework
---
## 개념

이 디자인 시스템은 **Next.js 기반 공통 UI 시스템**이다.

빠르게 프로토타입을 개발할 수 있는 TailwindCSS를 기반으로 시작했지만, 점진적으로 **CSS Module + BEM 구조**로 이관하여 **유지보수성과 다형성, 확장성**을 확보하는 방향으로 전환되었다.

**설계 초기부터 variant 기반 스타일링 시스템을 중심으로 설계되었고**, 모든 컴포넌트는 구조적으로 이 variant 시스템을 통해 표현된다.

---

## cn 유틸 함수

```tsx
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]): string {
  return twMerge(clsx(...inputs));
}

```

---

## 설계 발전 단계

---

### 초반 – Tailwind 유틸리티 기반

- variant는 모두 Tailwind class 문자열로 직접 정의
- 반응형은 `md:`, `lg:` 같은 기본 프리픽스만 사용
- 유지보수성이 떨어지지만 개발 속도는 매우 빠름

```tsx
const buttonVariants = cva(
  "inline-flex items-center justify-center font-medium transition-colors rounded-md",
  {
    variants: {
      intent: {
        primary: "bg-blue-600 text-white hover:bg-blue-700",
        secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300",
        destructive: "bg-red-600 text-white hover:bg-red-700",
      },
      tone: {
        solid: "",
        outline: "border border-current bg-transparent",
        ghost: "bg-transparent text-inherit hover:bg-gray-100",
      },
      size: {
        sm: "text-sm px-3 py-1.5",
        md: "text-base px-4 py-2",
        lg: "text-lg px-5 py-3",
      },
    },
  }
);

```

---

### 중반 – Tailwind + CSS Module 병행

- variant 일부는 CSS Module 사용, 일부는 여전히 Tailwind 유틸리티 사용
- `@apply`는 purge 이슈로 중단하고 클래스 이름만 모듈에서 관리
- 고급 스타일(애니메이션, 반응형)은 `.module.css`로 이동

```tsx
const buttonVariants = cva(styles.button, {
  variants: {
    intent: {
      primary: styles["button--primary"],
      secondary: "bg-gray-200 text-gray-800 hover:bg-gray-300",
      destructive: styles["button--destructive"],
    },
    tone: {
      solid: "",
      outline: styles["button--outline"],
      ghost: "bg-transparent text-inherit hover:bg-gray-100",
    },
    size: {
      sm: styles["button--sm"],
      md: styles["button--md"],
      lg: styles["button--lg"],
    },
  },
});

```

```css
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-weight: 500;
  transition: background-color 0.2s ease-in-out;
  border-radius: 0.375rem;
}

.button--primary {
  background-color: var(--color-primary);
  color: white;
}

.button--destructive {
  background-color: var(--color-danger);
  color: white;
}

.button--outline {
  border: 1px solid currentColor;
  background-color: transparent;
}

.button--sm {
  padding: 0.25rem 0.75rem;
}
.button--md {
  padding: 0.5rem 1rem;
}
.button--lg {
  padding: 0.75rem 1.25rem;
}
```

---

### 후반 – 완전한 CSS Module 중심

- 모든 스타일은 `.module.css`에서 관리
- Tailwind는 거의 제거, 스타일 토큰은 모두 CSS 변수로 선언

```tsx
import { cn } from "@/lib/utils";
import styles from "@/styles/ui/Button.module.css";
import { cva, type VariantProps } from "class-variance-authority";
import React from "react";

const buttonVariants = cva(styles.button, {
  variants: {
    intent: {
      primary: styles["button--primary"],
      secondary: styles["button--secondary"],
      destructive: styles["button--destructive"],
    },
    tone: {
      solid: styles["button--solid"],
      outline: styles["button--outline"],
      ghost: styles["button--ghost"],
    },
    size: {
      sm: styles["button--sm"],
      md: styles["button--md"],
      lg: styles["button--lg"],
    },
  },
  defaultVariants: {
    intent: "primary",
    tone: "solid",
    size: "md",
  },
});

export type ButtonProps = React.ButtonHTMLAttributes<HTMLButtonElement> & {
  variantIntent?: VariantProps<typeof buttonVariants>["intent"];
  variantTone?: VariantProps<typeof buttonVariants>["tone"];
  variantSize?: VariantProps<typeof buttonVariants>["size"];
};

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variantIntent, variantTone, variantSize, ...props }, ref) => {
    const baseClass = buttonVariants({
      intent: variantIntent,
      tone: variantTone,
      size: variantSize,
    });

    return <button ref={ref} className={cn(baseClass, className)} {...props} />;
  }
);

Button.displayName = "Button";

```

```css
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-weight: var(--font-medium);
  border-radius: var(--radius);
  transition: background-color 0.2s ease-in-out;
  padding: var(--space-md);
}

.button--primary {
  background-color: var(--color-primary);
  color: var(--color-text-inverse);
}

.button--secondary {
  background-color: var(--color-muted);
  color: var(--color-text);
}

.button--destructive {
  background-color: var(--color-danger);
  color: var(--color-text-inverse);
}

.button--outline {
  background-color: transparent;
  border: 1px solid currentColor;
}

.button--ghost {
  background-color: transparent;
  color: inherit;
}

.button--sm {
  font-size: var(--text-sm);
  padding: var(--space-sm) var(--space-md);
}

.button--md {
  font-size: var(--text-base);
  padding: var(--space-md) var(--space-lg);
}

.button--lg {
  font-size: var(--text-lg);
  padding: var(--space-lg) var(--space-xl);
}
```

---

## IconButton 확장 – Variant 상속 + 자체 Variant 병합

`IconButton`은 `Button`의 variant 시스템을 하위로 주입하면서, 자체적인 `shape`, `emphasis`, `size` variant 시스템을 갖는다.

```tsx
import { Button, ButtonProps } from "@/components/ui/Button";
import { cn } from "@/lib/utils";
import styles from "@/styles/custom/IconButton.module.css";
import { cva, type VariantProps } from "class-variance-authority";
import React from "react";

const iconButtonVariants = cva(styles.iconButton, {
  variants: {
    shape: {
      circle: styles["iconButton--circle"],
      square: styles["iconButton--square"],
    },
    emphasis: {
      subtle: styles["iconButton--subtle"],
      strong: styles["iconButton--strong"],
    },
    size: {
      sm: styles["iconButton--sm"],
      md: styles["iconButton--md"],
      lg: styles["iconButton--lg"],
    },
  },
  defaultVariants: {
    shape: "circle",
    emphasis: "subtle",
    size: "md",
  },
});

type IconButtonVariantProps = VariantProps<typeof iconButtonVariants>;

type IconButtonProps = Omit<ButtonProps, "children"> &
  IconButtonVariantProps & {
    icon: React.ReactElement;
    "aria-label": string;
    title: string;
    variantIntent?: ButtonProps["variantIntent"];
    variantTone?: ButtonProps["variantTone"];
    variantSize?: ButtonProps["variantSize"];
  };

export const IconButton = React.forwardRef<HTMLButtonElement, IconButtonProps>(
  (
    {
      className,
      icon,
      variantIntent,
      variantTone,
      variantSize,
      variantShape,
      variantEmphasis,
      variantSize: variantIconSize,
      "aria-label": ariaLabel,
      title,
      ...props
    },
    ref
  ) => {
    const iconClass = iconButtonVariants({
      shape: variantShape,
      emphasis: variantEmphasis,
      size: variantIconSize,
    });

    return (
      <Button
        ref={ref}
        variantIntent={variantIntent}
        variantTone={variantTone}
        variantSize={variantSize ?? variantIconSize}
        {...props}
        className={cn(iconClass, className)}
        aria-label={ariaLabel}
        title={title}
      >
        {icon}
      </Button>
    );
  }
);

IconButton.displayName = "IconButton";

```

```css
.iconButton {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  transition: background-color 0.2s ease-in-out;
}

.iconButton--circle {
  border-radius: 9999px;
}

.iconButton--square {
  border-radius: var(--radius);
}

.iconButton--subtle {
  background-color: transparent;
  color: var(--color-icon-muted);
}

.iconButton--strong {
  background-color: var(--color-surface);
  color: var(--color-icon);
  box-shadow: var(--shadow-sm);
}

.iconButton--sm {
  width: 2rem;
  height: 2rem;
  font-size: var(--text-sm);
}

.iconButton--md {
  width: 2.5rem;
  height: 2.5rem;
  font-size: var(--text-base);
}

.iconButton--lg {
  width: 3rem;
  height: 3rem;
  font-size: var(--text-lg);
}

```

---

## 따라서

초기에는 `.module.css` 안에 `@apply`를 사용해 Tailwind 클래스를 재사용하려 했지만, Tailwind v4의 purge는 이를 감지하지 못했고, **스타일이 실제로 제거되어 적용되지 않는 문제가 발생**했다.

결국 `.module.css` 내에 **클래스 이름만 정의하고**, 모든 스타일은 CSS 변수와 독립 모듈 기반으로 처리할 수밖에 없었다. 그리고 variant abstraction 시스템은 유지한 채 각 컴포넌트마다 이를 상속하거나 커스터마이징하도록 구성했다.

**Tailwind는 도구일 뿐이고, 구조의 중심은 variant 시스템과 module.css 기반 스타일링으로 수렴될 수밖에 없다.**