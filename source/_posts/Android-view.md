---
title: Android 自定义视图总结
date: 2017-03-25 19:41:39
categories: Android
tags:
    - Android View
---

# Android 自定义视图总结

---

[TOC]

很多在开发的过程中，经常会需要把某个UI视图给单独抽取出来，以便重复使用，下面举个简单例子，分析一下。

比如我们这边有个这样的视图，如下所示，显示一个订单模块中，经常显示一个商品的信息、数量以及价格。

![](https://raw.githubusercontent.com/LiushuiXiaoxia/GoodsDemo/master/doc/1.png)

上面的显示商品的实体是这样的。

```
public class GoodsItem implements Serializable {

    public String name;

    public int count;

    public double price;

    @Override
    public String toString() {
        return "GoodsItem{" +
                "name='" + name + '\'' +
                ", count=" + count +
                ", price=" + price +
                '}';
    }
}


GoodsItem goodsItem = new GoodsItem();
goodsItem.name = "可口可乐";
goodsItem.count = 123;
goodsItem.price = 321;
```

## 正常情况

正常情况下，我们如果只需要用一次，那么我们定义好布局就好了，然后简单的赋值就好，下面是代码。

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="50dp"
    android:gravity="center"
    android:orientation="horizontal"
    android:padding="10dp"
    tools:showIn="@layout/activity_main">

    <TextView
        android:id="@+id/tvName"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        tools:text="可口可乐" />

    <TextView
        android:id="@+id/tvCount"
        android:layout_width="80dp"
        android:layout_height="wrap_content"
        android:gravity="right"
        tools:text="x1" />

    <TextView
        android:id="@+id/tvPrice"
        android:layout_width="80dp"
        android:layout_height="wrap_content"
        android:gravity="right"
        tools:text="￥100" />
</LinearLayout>
```

```java
// normal
TextView tvName = (TextView) findViewById(R.id.tvName);
TextView tvCount = (TextView) findViewById(R.id.tvCount);
TextView tvPrice = (TextView) findViewById(R.id.tvPrice);
tvName.setText(goodsItem.name);
tvCount.setText(String.format("x%s", goodsItem.count));
tvPrice.setText(String.format("￥%s", goodsItem.price));
```

上面是最简单的方式，也是初学Android的时候常用的方式。

## Databinding

Google官方给出了一个Databinding的方式，这样我们代码了里面就可以少些很多代码，在对上面的代码进行少许优化后，就可以使用Databinding的方式。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:gravity="center"
        android:orientation="horizontal"
        android:padding="10dp"
        tools:showIn="@layout/activity_main">

        <TextView
            android:id="@+id/tvName"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            tools:text="可口可乐" />

        <TextView
            android:id="@+id/tvCount"
            android:layout_width="80dp"
            android:layout_height="wrap_content"
            android:gravity="right"
            tools:text="x1" />

        <TextView
            android:id="@+id/tvPrice"
            android:layout_width="80dp"
            android:layout_height="wrap_content"
            android:gravity="right"
            tools:text="￥100" />
    </LinearLayout>
</layout>
```

```java
// databind
binding.includeDatabinding.tvName.setText(goodsItem.name);
binding.includeDatabinding.tvCount.setText(String.format("x%s", goodsItem.count));
binding.includeDatabinding.tvPrice.setText(String.format("￥%s", goodsItem.price));
```

使用Databinding的最好好处，就不需要写烦人的findViewById了。

## Databinding升级

如果使用Databinding绑定的形式，那么赋值的方式就更加容易了，在定义xml的时候定义传递一个GoodsItem对象，然后在界面赋值一个对象就好了，简单明了。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="good"
            type="cn.mycommons.goodsdemo.GoodsItem" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:gravity="center"
        android:orientation="horizontal"
        android:padding="10dp"
        tools:showIn="@layout/activity_main">

        <TextView
            android:id="@+id/tvName"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="@{good.name}"
            tools:text="可口可乐" />

        <TextView
            android:id="@+id/tvCount"
            android:layout_width="80dp"
            android:layout_height="wrap_content"
            android:gravity="right"
            android:text='@{"x"+good.count}'
            tools:text="x1" />

        <TextView
            android:id="@+id/tvPrice"
            android:layout_width="80dp"
            android:layout_height="wrap_content"
            android:gravity="right"
            android:text='@{"￥"+good.price}'
            tools:text="￥100" />
    </LinearLayout>
</layout>
```
```java
// databind with param
binding.includeDatabindingWithParam.setGood(goodsItem);
```

## 自定义View

刚刚的示例比较，可以使用简单复制就可以搞定，有时候，业务比较复制，可能还要处理手势事件，如果使用Databinding就不怎么方便了。
所以自定义View是我们另外的一种方式。

首先我们顶一个自定义的View，这个view是专门用来显示商品信息的。

```java
public class GoodsItemView extends FrameLayout {

    private TextView tvName, tvCount, tvPrice;

    public GoodsItemView(Context context) {
        super(context);

        init();
    }

    public GoodsItemView(Context context, AttributeSet attrs) {
        super(context, attrs);

        init();
    }

    void init() {
        LayoutInflater.from(getContext()).inflate(R.layout.databinding, this);

        tvName = (TextView) findViewById(R.id.tvName);
        tvCount = (TextView) findViewById(R.id.tvCount);
        tvPrice = (TextView) findViewById(R.id.tvPrice);
    }

    public void updateUI(GoodsItem goodsItem) {
        tvName.setText(goodsItem.name);
        tvCount.setText(String.format("x%s", goodsItem.count));
        tvPrice.setText(String.format("￥%s", goodsItem.price));
    }
}
```

然在布局中引入就可以了，最好在Activity中找到所对应的对象，最后赋值就可以了。

```
<cn.mycommons.goodsdemo.GoodsItemView
    android:id="@+id/goodsItemView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
    
// custom view
GoodsItemView goodsItemView = (GoodsItemView) findViewById(R.id.goodsItemView);
goodsItemView.updateUI(goodsItem);
```

## 自定义Module

有时候一个自定义View会有所限制，比如，自定义View不能代码混淆，而且自定义View会受到分类的限制。
有时候仅仅只是一处使用，单独提取一个View代价太大，那么我们可以单独自定义一个Module的方式，这样做的话，可以减少Activity的代码量。
话不多说，先看代码。

首先商品展示界面还在定义在Activity的布局中。

```
<include
    android:id="@+id/includeModule"
    layout="@layout/databinding" />
```

然后我们定义一个通用的Module接口，以及实现类。

```java
public interface IModule {

    void create();
    void destroy();
}

public class GoodsItemModule implements IModule {
    @NonNull
    private final View rootView;
    private TextView tvName, tvCount, tvPrice;

    public GoodsItemModule(@NonNull View rootView) {
        this.rootView = rootView;
    }

    @Override
    public void create() {
        tvName = (TextView) rootView.findViewById(R.id.tvName);
        tvCount = (TextView) rootView.findViewById(R.id.tvCount);
        tvPrice = (TextView) rootView.findViewById(R.id.tvPrice);
    }

    public void updateUI(GoodsItem goodsItem) {
        tvName.setText(goodsItem.name);
        tvCount.setText(String.format("x%s", goodsItem.count));
        tvPrice.setText(String.format("￥%s", goodsItem.price));
    }

    @Override
    public void destroy() {
        tvName = null;
        tvCount = null;
        tvPrice = null;
    }
}
```

然后我们在Activity中，把商品展示的接的根视图传入到Module中，这样对商品展示的所有逻辑都是写在了GoodsItemModule中了，
这样就减少了耦合，Activity的代码量也比较少，同时 Module中定义了简单的生命周期，可以在Activity中调用，方便管理。

```
// module
goodsItemModule = new GoodsItemModule(findViewById(R.id.includeModule));
goodsItemModule.create();
goodsItemModule.updateUI(goodsItem);

// 这一段可以在Activity.onDestroy中执行
goodsItemModule.destroy();
```

## Fragment

看到上面了方法，是不是想到了Fragment，其实Fragment也是Google提供的自定义Module的一种实现。Fragment提供更加完善的生命周期，不过也是一个值得吐槽的地方。
导致很多实用Fragment的时候不知道怎么用，以及实用过于复杂。

下面展示下Fragment的使用。

```java
public class GoodsItemFragment extends Fragment {

    static final String EXTRA_GOODS_ITEM = "goods_item";

    public static GoodsItemFragment newFragment(GoodsItem goodsItem) {
        GoodsItemFragment fragment = new GoodsItemFragment();
        Bundle args = new Bundle();
        args.putSerializable(EXTRA_GOODS_ITEM, goodsItem);
        fragment.setArguments(args);
        return fragment;
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater,
                             @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.databinding, container, false);
    }

    @Override
    public void onViewCreated(View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);

       TextView tvName = (TextView) view.findViewById(R.id.tvName);
       TextView tvCount = (TextView) view.findViewById(R.id.tvCount);
       TextView tvPrice = (TextView) view.findViewById(R.id.tvPrice);

        GoodsItem goodsItem = (GoodsItem) getArguments().getSerializable(EXTRA_GOODS_ITEM);
        if (goodsItem != null) {
            tvName.setText(goodsItem.name);
            tvCount.setText(String.format("x%s", goodsItem.count));
            tvPrice.setText(String.format("￥%s", goodsItem.price));
        }
    }
}
```

以及Activity中的调用方式。

```java
// fragment
getSupportFragmentManager()
        .beginTransaction()
        .add(binding.fragmentContainer.getId(), GoodsItemFragment.newFragment(goodsItem))
        .commit();
```

## 总结

上面展示了Android中自定义视图模块的常用方式，下面进行比较和总结：

* 正常情况 : 简单明了，所有的开发人员都会，只不过代码比较冗余，不利于解耦。
* Databinding : 简单明了，减少繁琐的findViewById代码，而且方便。
* Databinding升级 : 更加简单明了，不过有少许的学习成本，不过现在已经是Android程序员的基本技能了。
* 自定义View : 代码稍微复杂，开发繁琐，同时自定义View不能进行代码混淆，可以根据View的名字，猜想逻辑。
* 自定义Module : 这种方式，简单粗暴，学习成本比较低，小项目内可以自由使用，不过不利于推广。
* Fragment : 高级的自定义Module方式，有学习成本，利于推广和维护。

上面的自定义View和自定义Module是两种程序的实习方式，有句话叫做组合由于继承，所以有时候，我会选择后者。

个人的喜爱程度是这样，当然有时候会根据项目的实际情况进行选择，也有时候会进行组合使用。

Databinding升级 = Databinding > Fragment > 自定义Module > 正常情况 > 自定义View

以上就是作者对Android自定义视图总结，欢迎前来讨论。

[示例代码地址](https://github.com/LiushuiXiaoxia/GoodsDemo)