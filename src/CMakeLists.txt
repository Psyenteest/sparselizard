set(SRC_DIR_LIST
  ${CMAKE_CURRENT_SOURCE_DIR}
  ${CMAKE_CURRENT_SOURCE_DIR}/resolution
  ${CMAKE_CURRENT_SOURCE_DIR}/shapefunction
  ${CMAKE_CURRENT_SOURCE_DIR}/shapefunction/lagrange
  ${CMAKE_CURRENT_SOURCE_DIR}/shapefunction/hierarchical
  ${CMAKE_CURRENT_SOURCE_DIR}/shapefunction/hierarchical/h1
  ${CMAKE_CURRENT_SOURCE_DIR}/shapefunction/hierarchical/hcurl
  ${CMAKE_CURRENT_SOURCE_DIR}/mesh
  ${CMAKE_CURRENT_SOURCE_DIR}/formulation
  ${CMAKE_CURRENT_SOURCE_DIR}/field
  ${CMAKE_CURRENT_SOURCE_DIR}/io
  ${CMAKE_CURRENT_SOURCE_DIR}/io/nastran
  ${CMAKE_CURRENT_SOURCE_DIR}/io/paraview
  ${CMAKE_CURRENT_SOURCE_DIR}/io/gmsh
  ${CMAKE_CURRENT_SOURCE_DIR}/geometry
  ${CMAKE_CURRENT_SOURCE_DIR}/expression
  ${CMAKE_CURRENT_SOURCE_DIR}/expression/operation
  ${CMAKE_CURRENT_SOURCE_DIR}/gausspoint)

custom_add_library_from_dir(sparselizard "${SRC_DIR_LIST}")

custom_copy_file(sparselizard "${SRC_DIR_LIST}" "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/include" "*.h")

target_include_directories(sparselizard PUBLIC ${SRC_DIR_LIST})

target_compile_features(sparselizard PRIVATE cxx_std_11)
target_compile_options(sparselizard PUBLIC -fopenmp -fPIC -no-pie -O3)

ConfigureBLAS(sparselizard)
ConfigureGMSH(sparselizard)
ConfigureMETIS(sparselizard)
if(${USE_MPI})
    ConfigureMPI(sparselizard)
endif()
ConfigureMUMPS(sparselizard)
ConfigurePETSC(sparselizard)
ConfigureSLEPC(sparselizard)

# Optional for std::thread
# find_package(Threads)
# target_link_libraries(sparselizard PUBLIC ${CMAKE_THREAD_LIBS_INIT})

if(NOT (${BLAS_FOUND} AND ${METIS_FOUND} AND ${MUMPS_FOUND} AND ${PETSC_FOUND} AND ${SLEPC_FOUND}))
    message(FATAL_ERROR "\nAt least one required package (blas, metis, mumps, petsc, slepc) not found.")
endif()

# set configure file
if(${BLAS_FOUND})
    add_definitions(-DHAVE_BLAS)
endif()
if(${GMSH_FOUND})
    add_definitions(-DHAVE_GMSH)
endif()
if(${METIS_FOUND})
    add_definitions(-DHAVE_METIS)
endif()
if(${MPI_FOUND})
    add_definitions(-DHAVE_MPI)
endif()
if(${MUMPS_FOUND})
    add_definitions(-DHAVE_MUMPS)
endif()
if(${PETSC_FOUND})
    add_definitions(-DHAVE_PETSC)
endif()
if(${SLEPC_FOUND})
    add_definitions(-DHAVE_SLEPC)
endif()

target_include_directories(sparselizard PUBLIC ${CMAKE_CURRENT_BINARY_DIR})

