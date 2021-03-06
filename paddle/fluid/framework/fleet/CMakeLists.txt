if(WITH_PSLIB)
    cc_library(fleet_wrapper SRCS fleet_wrapper.cc DEPS framework_proto variable_helper scope pslib_brpc pslib)
else()
    cc_library(fleet_wrapper SRCS fleet_wrapper.cc DEPS framework_proto variable_helper scope)
endif(WITH_PSLIB)

if(WITH_HETERPS)
    if(WITH_NCCL)
        nv_library(ps_gpu_wrapper SRCS ps_gpu_wrapper.cu ps_gpu_wrapper.cc
        DEPS heter_ps)
        add_subdirectory(heter_ps)
    elseif(WITH_RCCL)
        hip_library(ps_gpu_wrapper SRCS ps_gpu_wrapper.cu ps_gpu_wrapper.cc
        DEPS heter_ps)
        add_subdirectory(heter_ps)
    endif(WITH_NCCL)
else()
    cc_library(ps_gpu_wrapper SRCS ps_gpu_wrapper.cc)
endif(WITH_HETERPS)

if(WITH_NCCL OR WITH_RCCL)
    cc_library(nccl_wrapper SRCS nccl_wrapper.cc DEPS framework_proto variable_helper scope)
endif()
if(WITH_BOX_PS)
    if(WITH_GPU)
        nv_library(box_wrapper SRCS box_wrapper.cc box_wrapper.cu DEPS framework_proto lod_tensor box_ps)
    endif()
    if(WITH_ROCM)
        hip_library(box_wrapper SRCS box_wrapper.cc box_wrapper.cu DEPS framework_proto lod_tensor box_ps)
    endif()
else()
    cc_library(box_wrapper SRCS box_wrapper.cc DEPS framework_proto lod_tensor)
endif(WITH_BOX_PS)


if(WITH_GLOO)
    cc_library(gloo_wrapper SRCS gloo_wrapper.cc DEPS framework_proto variable_helper scope gloo)
else()
    cc_library(gloo_wrapper SRCS gloo_wrapper.cc DEPS framework_proto variable_helper scope)
endif(WITH_GLOO)

cc_library(heter_wrapper SRCS heter_wrapper.cc DEPS framework_proto device_context heter_service_proto)

cc_test(test_fleet_cc SRCS test_fleet.cc DEPS fleet_wrapper gloo_wrapper fs shell)

if(WITH_ASCEND)
    cc_library(ascend_wrapper SRCS ascend_wrapper.cc DEPS framework_proto lod_tensor ascend_ge ascend_graph)
endif(WITH_ASCEND)
