# data_test

Reference data for kornia's test suite. kornia's `conftest.py` `data` fixture loads these files, pinned by commit SHA in
[`testing/reference_data.py`](https://github.com/kornia/kornia/blob/main/testing/reference_data.py).

## Formats

The `.safetensors` files are the current format. kornia reads them with `kornia.core.load_safetensors`, with no pickle
and no extra dependency.

The `.pt` files are the original pickles. They stay because older kornia commits pin them by SHA. Each `.safetensors`
file holds the same tensors as the `.pt` file with the same stem: same dtype, same shape, and the same bit pattern.
Its `__metadata__` names the source blob under `converted_from`.

What the conversion changed besides the container:

- String entries (`source`, `Original_README`) moved to `__metadata__` under the same key.
- `knchurch_disk.pt` stores `disk1`/`disk2` as a one-element `list[DISKFeatures]`. They are flattened to
  `disk1.keypoints`, `disk1.descriptors` and `disk1.detection_scores`, and the same for `disk2`. kornia's fixture
  rebuilds the list.
- `loftr_outdoorr_tentatives1` (a misspelled key) was dropped from `loftr_outdoor_and_homography_data`. It is bitwise
  equal to `loftr_outdoor_tentatives1`.
- Every tensor is stored contiguous, and `requires_grad` is not stored.

## Files

| file | tensors | origin |
|---|---|---|
| `loftr_outdoor_and_homography_data` | `image0`, `image1` f32 (1,1,450,600); `H_gt` f64 (3,3); `pts0`, `pts1` f64 (8,2); `loftr_outdoor_tentatives0/1` f32 (1719,2) | image pair and manually annotated correspondences from the CMP two-view homography set, Lebeda et al. 2012 (`source` in the metadata); LoFTR outdoor tentative matches |
| `loftr_indoor_and_fundamental_data` | `image0`, `image1` f32 (1,1,768,576); `F_gt` f64 (3,3); `pts0`, `pts1` f64 (10,2); `loftr_{indoor,outdoor}_tentatives0/1` f32 (639,2) | image pair and manually annotated correspondences from the CMP two-view epipolar-geometry set, Lebeda et al. 2012 (`source`, `Original_README` in the metadata); LoFTR indoor and outdoor tentative matches |
| `adalam_test` | `lafs1` f32 (1,3206,2,3), `lafs2` f32 (1,4119,2,3); `descs1` f32 (3206,128), `descs2` f32 (4119,128); `expected_idxs` i64 (46,2); `lightglue_disk_conf` f32 (68,1); `lightglue_disk_idxs` i64 (68,2) | local features and the AdaLAM and LightGlue (DISK) reference matches |
| `knchurch_disk` | `img1`, `img2` f32 (1,3,480,320); `K1`, `K2`, `R1`, `R2`, `E`, `Fm` f64 (1,3,3); `t1`, `t2` f64 (1,3,1); `disk{1,2}.keypoints` f32 (256,2), `disk{1,2}.descriptors` f32 (256,128), `disk{1,2}.detection_scores` f32 (256,) | the `knchurch` image pair with intrinsics, poses, `E` and `F`; DISK (`depth`) reference outputs, 256 features |
| `xfeat_reference` | `img1`, `img2` f32 (1,3,480,320), with the same bits as `knchurch_disk`'s; `xfeat_kpts{0,1}` f32 (1024,2), `xfeat_scores{0,1}` f32 (1024,), `xfeat_desc{0,1}` f32 (1024,64); `lighterglue_matches{0,1}` i64 (1024,), `lighterglue_mscores{0,1}` f32 (1024,) | XFeat (`top_k=1024`) and LighterGlue reference outputs on the `knchurch` pair |
