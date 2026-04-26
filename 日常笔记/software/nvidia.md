# nvidia及内存和cpu的调优设置

调整功耗：

```bash
nvidia-smi -rgc; nvidia-smi -rmc; nvidia-smi -pl 450; nvidia-smi -gtt 84
```

以上是默认，现在调整到最佳：

```bash
nvidia-smi -pl 600; nvidia-smi -gtt 88; nvidia-smi -lgc 3105; nvidia-smi -lmc 10501
```

cpu 配置

```bash
powercfg /setacvalueindex 66666666-6666-6666-6666-666666666666 SUB_PROCESSOR PROCTHROTTLEMIN 5; 

powercfg /setdcvalueindex 66666666-6666-6666-6666-666666666666 SUB_PROCESSOR PROCTHROTTLEMIN 5; 

powercfg /setacvalueindex 66666666-6666-6666-6666-666666666666 SUB_PROCESSOR IDLEDISABLE 0; 

powercfg /setdcvalueindex 66666666-6666-6666-6666-666666666666 SUB_PROCESSOR IDLEDISABLE 0; 

powercfg /setacvalueindex 66666666-6666-6666-6666-666666666666 SUB_PROCESSOR PERFINCPOL 3; 

powercfg /setactive 66666666-6666-6666-6666-666666666666
```
