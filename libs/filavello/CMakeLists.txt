# /*
#  * Copyright (C) 2024 The Android Open Source Project
#  *
#  * Licensed under the Apache License, Version 2.0 (the "License");
#  * you may not use this file except in compliance with the License.
#  * You may obtain a copy of the License at
#  *
#  *      http://www.apache.org/licenses/LICENSE-2.0
#  *
#  * Unless required by applicable law or agreed to in writing, software
#  * distributed under the License is distributed on an "AS IS" BASIS,
#  * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  * See the License for the specific language governing permissions and
#  * limitations under the License.
#  */
# Note: Copyright header above included to follow CODE_STYLE. It's unclear why this is 
# not included in previous CMakeLists.txt files

cmake_minimum_required(VERSION 3.19)
project(filavello)

set(TARGET filavello)
set(PUBLIC_HDR_DIR include)

# Use https://corrosion-rs.github.io/corrosion to create a Rust code
include(FetchContent)

FetchContent_Declare(
    Corrosion
    GIT_REPOSITORY https://github.com/corrosion-rs/corrosion.git
    GIT_TAG 64289b1d79d6d19cd2e241db515381a086bb8407 # v0.5 version
)
FetchContent_MakeAvailable(Corrosion)

# This CMakeLists.txt is largely guesswork

# ==================================================================================================
# Sources and headers
# ==================================================================================================

set(PUBLIC_HDRS
    ${PUBLIC_HDR_DIR}/${TARGET}/TextRenderer.h
)

set(RUST_CRATE_SOURCE_TOML
    crate/Cargo.toml
)

# ==================================================================================================
# Include and target definitions
# ==================================================================================================

include_directories(${PUBLIC_HDR_DIR})

corrosion_import_crate(MANIFEST_PATH ${RUST_CRATE_SOURCE_TOML})

target_include_directories(${TARGET} INTERFACE ${PUBLIC_HDR_DIR})

target_sources(${TARGET}
        INTERFACE
        ${PUBLIC_HDRS}
)

set_target_properties(${TARGET} PROPERTIES FOLDER Libs)

# ==================================================================================================
# Compiler flags
# ==================================================================================================

# ==================================================================================================
# Installation
# ==================================================================================================
corrosion_install(TARGETS ${TARGET} ARCHIVE DESTINATION lib/${DIST_DIR})
install(DIRECTORY ${PUBLIC_HDR_DIR}/${TARGET} DESTINATION include)
