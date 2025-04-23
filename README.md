# yolov5s训练自己的数据集

## 1.使用yolov5s的训练命令

```
python train.py --data data/voc_bm.yaml --cfg models/yolov5s_bm.yaml --weights weights/yolov5s.pt --batch-size 16 --epochs 100 --workers 4 --name yolov5s-baseline
```

## 2.训练过程可视化

```
tensorboard --logdir=./runs/train/yolov5s-baseline
```

## 3.测试训练出的网络模型

### 3.1使用yolov5s训练出的权重测试图片

```
python detect.py --source ./testfiles/img1.jpg --weights runs/train/yolov5s-baseline/weights/best.pt  
```

注：1) 批量处理文件夹下的图片和视频可以指定文件夹的名字，如--source ./testfiles

2)命令后可加上目标的置信度阈值，如--conf-thres 0.4

### 3.2使用yolov5s训练出的权重做性能统计

```
python val.py --data data/voc_bm.yaml --weights runs/train/yolov5s-baseline/weights/best.pt --batch-size 16
```









# yolov5m训练

## 1.训练命令

```
python train.py --data data/voc_bm.yaml --cfg models/yolov5m_bm.yaml --weights weights/yolov5m.pt --batch-size 16 --epochs 100 --workers 4 --name yolov5m-baseline
```

## 2.训练可视化

```
tensorboard --logdir=./runs/train/yolov5m-baseline
```

## 3.性能统计

```
python val.py --data data/voc_bm.yaml --weights runs/train/yolov5m-baseline/weights/best.pt --batch-size 16
```





# 知识蒸馏训练

把runs/train/yolov5s-baseline/weights/best.pt和runs/train/yolov5m-baseline/weights/best.pt拷贝到weights文件夹下并改名为yolov5s_bm.pt和yolov5m_bm.pt

## 1.训练命令

```
python train_distillation.py --weights weights/yolov5s_bm.pt --cfg models/yolov5s_bm.yaml --data data/voc_bm.yaml --batch-size 8 --epochs 100 --workers 4 --t_weights weights/yolov5m_bm.pt --hyp data/hyps/hyp.scratch-low-distillation.yaml --distill --dist_loss l2 --name yolov5s-distilled
```

注意batch-size大时可能有显存溢出
训练参数:
--weights：预训练模型
--t_weights：教师模型权重
--distill：使用知识蒸馏进行训练
--dist_loss：l2或者kl（默认l2）
--temperature：知识蒸馏时的温度（默认20）

## 2.性能评估

```
python val.py --data data/voc_bm.yaml --weights runs/train/yolov5s-distilled/weights/best.pt --batch-size 16
```

