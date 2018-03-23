# HIFI 定点库使用 {#6-hifi-定点库使用}

前面五章是基础，接下来讲几个常用的 hifi 定点库 API 的基础例子。相关的 API 手册请向**cadence**索取或联系我们的**FAE**。

* 求正余弦

```
int sin_in = (1.0/6) * (unsigned)(1<<31);
int scl_sin_i = scl_sine32x32(sin_in);
printf("scl_sin_i: %f\n", (float)scl_sin_i/(unsigned)(1<<31));

int cos_in = (1.0/3) * (unsigned)(1<<31);
int scl_cos_i = scl_cosine32x32(cos_in);
printf("scl_cos_i: %f\n", (float)scl_cos_i/(unsigned)(1<<31));

```

* fft 具体使用可以访问[vsp\_simulate](https://github.com/NationalChip/vsp_simulate)。

```
// sample 为 Qn 由用户定义
int FFT(int *sample, int *real, int *image, int frame_size, int fft_size)
{
    int bexp = 0;
    int shift_num = 0;
    int *x = (int *)malloc(sizeof(int) * fft_size);
    int *fft_cpl_out = (int *)malloc(sizeof(int) * (fft_size + 2));

    if (x == NULL || fft_cpl_out == NULL) {
        return -1;
    }

    fft_handle_t h = h_fft_real_x32_512;

    memcpy(x, sample, frame_size*sizeof(int));
    for(int i=frame_size; i
<
fft_size; i++) {
        x[i] = 0;
    }

    shift_num = fft_real32x32(fft_cpl_out, x, bexp, h);

    for(int i=0; i
<
fft_size/2+1; i++) {
        real[i] = fft_cpl_out[2*i];  // Qn - shift_num
        image[i] = fft_cpl_out[2*i + 1]; // Qn - shift_num
    }

    free(x);
    free(fft_cpl_out);

    return shift_num;
}

```

* 还有一些其它例子见开源工程[vsp\_simulate](https://github.com/NationalChip/vsp_simulate)。



