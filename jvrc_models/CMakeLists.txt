cmake_minimum_required(VERSION 2.8.3)
project(jvrc_models)

find_package(catkin REQUIRED COMPONENTS roslint)

catkin_package(CFG_EXTRAS jvrc_models-extras.cmake)

if (EXISTS ${PROJECT_SOURCE_DIR}/model)
  execute_process(COMMAND git pull
  ## temporary fix version
#  execute_process(COMMAND git checkout 540ab2709d7d491a88b1a716aac085ae91e0889e
#  execute_process(COMMAND git checkout 978fea941ce938a29a1765dbb170ede41e3533aa
#  execute_process(COMMAND git checkout 69a151665f6400fda1e69f2361adfb7cb2d30c7e
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}/model)
else ()
  execute_process(COMMAND git clone https://github.com/jvrc/model.git
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR})
endif()

# lint for check existence of tabs
set(ROSLINT_VRML_CMD cpplint)
set(ROSLINT_VRML_OPTS "")

file(GLOB_RECURSE MAIN_VRML_FILES *main*.wrl)
foreach(MAIN_VRML_FILE ${MAIN_VRML_FILES})
  get_filename_component(MODEL_DIR_NAME ${MAIN_VRML_FILE} DIRECTORY)
  file(TO_NATIVE_PATH ${MODEL_DIR_NAME}/CPPLINT.cfg LINT_FILE)
  if(EXISTS ${LINT_FILE})
    message("execute lint in ${MAIN_VRML_FILE}")
    roslint_custom("${ROSLINT_VRML_CMD}" "${ROSLINT_VRML_OPTS}" ${MAIN_VRML_FILE})
  endif()
endforeach()
