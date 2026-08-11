# ENV variable
set nnUNet_raw=.\nnUNet_raw
set nnUNet_preprocessed=.\nnUNet_preprocessed
set nnUNet_results=.\nnUNet_results

# Pretraining on Brats 2021 - Task 1

## Run the fingerprint and configuration script for the dataset's id 123 so nnU-Net can build a specialized plan:
```
nnUNetv2_plan_and_preprocess -d 123 --verify_dataset_integrity
```

## Training 
```
nnUNetv2_train 123 3d_fullres 0
nnUNetv2_train 123 3d_fullres 1
nnUNetv2_train 123 3d_fullres 2
nnUNetv2_train 123 3d_fullres 3
nnUNetv2_train 123 3d_fullres 4
```

## Fusion fold
```
nnUNetv2_find_best_configuration 123 -c 3d_fullres
```

## Inference
```
nnUNetv2_predict -i ./nnUNet_raw/CFB-GBM_test/imagesTr -o ./nnUNet_raw/CFB-GBM_test/PredictionsTr -d 123 -c 3d_fullres -f 0 1 2 3 4
```

## Evaluate prediction from ground truth
```
nnUNetv2_evaluate_folder ./nnUNet_raw/CFB-GBM_test/labelsTr ./nnUNet_raw/CFB-GBM_test/PredictionsTr -djfile ./nnUNet_raw/Dataset123_BraTS2Modality/dataset.json -pfile ./nnUNet_results/Dataset123_BraTS2Modality/nnUNetTrainer__nnUNetPlans__3d_fullres/plans.json
```

# Finetuning

## Adapt plan
```
nnUNetv2_move_plans_between_datasets -s 123 -t 124 -sp nnUNetPlans -tp nnUNetPlans
```

## Run the fingerprint and configuration script so nnU-Net can build a specialized plan for your hospital's spatial geometry:
```
nnUNetv2_preprocess -d 124 -plans_name nnUNetPlans -c 3d_fullres
```

## Run the finetuning
```
nnUNetv2_train 124 3d_fullres 0 -tr nnUNetTrainer_100epochs -pretrained_weights ./nnUNet_results/Dataset123_BraTS2Modality/nnUNetTrainer__nnUNetPlans__3d_fullres/fold_0/checkpoint_final.pth
nnUNetv2_train 124 3d_fullres 1 -tr nnUNetTrainer_100epochs -pretrained_weights ./nnUNet_results/Dataset123_BraTS2Modality/nnUNetTrainer__nnUNetPlans__3d_fullres/fold_1/checkpoint_final.pth
nnUNetv2_train 124 3d_fullres 2 -tr nnUNetTrainer_100epochs -pretrained_weights ./nnUNet_results/Dataset123_BraTS2Modality/nnUNetTrainer__nnUNetPlans__3d_fullres/fold_2/checkpoint_final.pth
nnUNetv2_train 124 3d_fullres 3 -tr nnUNetTrainer_100epochs -pretrained_weights ./nnUNet_results/Dataset123_BraTS2Modality/nnUNetTrainer__nnUNetPlans__3d_fullres/fold_3/checkpoint_final.pth
nnUNetv2_train 124 3d_fullres 4 -tr nnUNetTrainer_100epochs -pretrained_weights ./nnUNet_results/Dataset123_BraTS2Modality/nnUNetTrainer__nnUNetPlans__3d_fullres/fold_4/checkpoint_final.pth
```

## Finalize cross validation perf
```
nnUNetv2_find_best_configuration 124 -c 3d_fullres -tr nnUNetTrainer_100epochs
```

## Inference on finetuned test set
```
nnUNetv2_predict -i ./nnUNet_raw/Dataset125_CFB-GBM-finetune-test/imagesTr -o ./nnUNet_raw/Dataset125_CFB-GBM-finetune-test/PredictionsTr -d 124 -c 3d_fullres -tr nnUNetTrainer_100epochs -f 0 1 2 3 4
```

## Evaluate prediction from ground truth
```
nnUNetv2_evaluate_folder ./nnUNet_raw/Dataset125_CFB-GBM-finetune-test/labelsTr ./nnUNet_raw/Dataset125_CFB-GBM-finetune-test/PredictionsTr -djfile ./nnUNet_raw/Dataset124_CFB-GBM-finetune-train/dataset.json -pfile ./nnUNet_results/Dataset124_CFB-GBM-finetune-train/nnUNetTrainer_100epochs__nnUNetPlans__3d_fullres/plans.json
```

## Inference on finetuned fix test set
```
nnUNetv2_predict -i ./nnUNet_raw/Dataset126_CFB-GBM-finetune-test-fix/imagesTr -o ./nnUNet_raw/Dataset126_CFB-GBM-finetune-test-fix/PredictionsTr -d 124 -c 3d_fullres -tr nnUNetTrainer_100epochs -f 0 1 2 3 4
```

## Evaluate prediction from ground truth on fix test set
```
nnUNetv2_evaluate_folder ./nnUNet_raw/Dataset126_CFB-GBM-finetune-test-fix/labelsTr ./nnUNet_raw/Dataset126_CFB-GBM-finetune-test-fix/PredictionsTr -djfile ./nnUNet_raw/Dataset124_CFB-GBM-finetune-train/dataset.json -pfile ./nnUNet_results/Dataset124_CFB-GBM-finetune-train/nnUNetTrainer_100epochs__nnUNetPlans__3d_fullres/plans.json
```

# Learning from scratch

## Preprocessing
```
nnUNetv2_plan_and_preprocess -d 127 --verify_dataset_integrity
```

## Initiating 5 folds training
```
nnUNetv2_train 127 3d_fullres 0 -tr nnUNetTrainer_100epochs && nnUNetv2_train 127 3d_fullres 1 -tr nnUNetTrainer_100epochs && nnUNetv2_train 127 3d_fullres 2 -tr nnUNetTrainer_100epochs && nnUNetv2_train 127 3d_fullres 3 -tr nnUNetTrainer_100epochs && nnUNetv2_train 127 3d_fullres 4 -tr nnUNetTrainer_100epochs
```

## Finalize cross validation perf
```
nnUNetv2_find_best_configuration 127 -c 3d_fullres -tr nnUNetTrainer_100epochs
```

## Inference on test set
```
nnUNetv2_predict -i ./nnUNet_raw/Dataset128_CFB-GBM-test-fix/imagesTr -o ./nnUNet_raw/Dataset128_CFB-GBM-test-fix/PredictionsTr -d 127 -c 3d_fullres -tr nnUNetTrainer_100epochs -f 0 1 2 3 4
```

## Evaluate prediction from ground truth
```
nnUNetv2_evaluate_folder ./nnUNet_raw/Dataset128_CFB-GBM-test-fix/labelsTr ./nnUNet_raw/Dataset128_CFB-GBM-test-fix/PredictionsTr -djfile ./nnUNet_raw/Dataset127_CFB-GBM-train/dataset.json -pfile ./nnUNet_results/Dataset127_CFB-GBM-train/nnUNetTrainer_100epochs__nnUNetPlans__3d_fullres/plans.json
```

# gen Final GTV
## Inference on test set
```
nnUNetv2_predict -i ./nnUNet_raw/CFB-GBM_gen/imagesTr -o ./nnUNet_raw/CFB-GBM_gen/PredictionsTr -d 127 -c 3d_fullres -tr nnUNetTrainer_100epochs -f 0 1 2 3 4
```

```
nnUNetv2_predict -i ./nnUNet_raw/CFB-GBM_gen/imagesTr -o ./nnUNet_raw/CFB-GBM_gen/PredictionsTr -d 124 -c 3d_fullres -tr nnUNetTrainer_100epochs -f 0 1 2 3 4
```