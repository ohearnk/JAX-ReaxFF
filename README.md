# JAX-ReaxFF
JAX-ReaxFF: A Gradient Based Framework for Extremely Fast Optimization of Reactive Force Fields

Existing parameter optimization methods for ReaxFF consist of black-box techniques using genetic algorithms or Monte-Carlo methods. Due to the stochastic behavior of these methods, the optimization process can require millions of error evaluations for complex parameter fitting tasks, significantly hampering the rapid development of high quality parameter sets. 

In this work, we present JAX-ReaxFF, a novel software tool that leverages modern machine learning infrastructure to enable extremely fast optimization of ReaxFF parameters. By calculating gradients of the loss function using the JAX library, we are able to utilize highly effective local optimization methods, such as the limited Broyden–Fletcher–Goldfarb–Shanno (LBFGS) and Sequential Least Squares Programming (SLSQP) methods. 

As a result of the performance portability of JAX, JAX-ReaxFF can execute efficiently on multi-core CPUs, GPUs (or even TPUs). By leveraging the gradient information and modern hardware accelerators, we are able to decrease parameter optimization time for ReaxFF from days to mere minutes. JAX-ReaxFF framework can also serve as a sandbox environment to explore customizing the ReaxFF functional form for more accurate modeling.

Paper: [Jax-ReaxFF](https://chemrxiv.org/engage/chemrxiv/article-details/61cecad57284d03c89fe39da)


## How to Install
Jax-ReaxFF requires JAX 0.1.76 and jaxlib 0.1.73 ([Jax Repo](https://github.com/google/jax)). <br>
Since the optimizer is highly more performant on GPUs, GPU version of jaxlib needs to be installed (GPU version supports both CPU and GPU execution). <br>

**1-** Before the installation, a supported version of CUDA and CuDNN are needed (for jaxlib). <br>

**2-** Cloning the Jax-ReaxFF repo:
```
git clone https://github.com/cagrikymk/Jax-ReaxFF
cd Jax-ReaxFF
```

**3-** The installation can be done in a conda environment:
```
conda create -n jax-env python=3.8
```
**4-** Then jaxlib competible with CUDA-11 and cuDNN-8.05 can be installed:
```
# install jaxlib-0.1.73 with Python 3.8, CUDA-11 and cuDNN-8.05 support
pip install https://storage.googleapis.com/jax-releases/cuda11/jaxlib-0.1.73+cuda11.cudnn805-cp38-none-manylinux2010_x86_64.whl
```
Other precompilations of jaxlib-0.1.73 compatible with different cuda, cudnn and Python versions can be found here: [Jax Releases](https://storage.googleapis.com/jax-releases/jax_releases.html) <br>

**5-** Finally, Jax-ReaxFF is installed:
```
python setup.py install
```
After the setup, Jax-ReaxFF can be accessed via command line interface(CLI) with **jaxreaxff**

To test the installation:
```
jaxreaxff --init_FF Datasets/cobalt/ffield_lit
		  --params Datasets/cobalt/params 
          --geo Datasets/cobalt/geo 
          --train_file Datasets/cobalt/trainset.in 
          --num_e_minim_steps 200 
          --e_minim_LR 1e-3 
          --out_folder ffields 
          --save_opt all 
          --num_trials 1 
          --num_steps 20
          --init_FF_type fixed
```
#### Potential Issues

On a HPC cluster, CUDA might be loaded somewhere different than /usr/local/cuda-xx.x. In this case, XLA compiler might not locate CUDA installation. 
To solve this, we can speficy the cuda directory using XLA_FLAGS:
```
# To see where cuda is installed
which nvcc # will print /opt/software/CUDAcore/11.1.1/bin/nvcc
export XLA_FLAGS="$XLA_FLAGS --xla_gpu_cuda_data_dir=/opt/software/CUDAcore/11.1.1"
```

Another potential issue related XLA compilation on clusters is *RuntimeError: Unknown: no kernel image is available for execution on the device* (potentially related to singularity)
and it can be solved by changing XLA_FLAGS to:

```
export XLA_FLAGS="$XLA_FLAGS --xla_gpu_force_compilation_parallelism=1"
```
This flag can increase the compilation time drastically.

