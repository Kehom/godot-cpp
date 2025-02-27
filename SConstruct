#!/usr/bin/env python

import os
import platform
import sys
import subprocess
from binding_generator import scons_generate_bindings, scons_emit_files


EnsureSConsVersion(4, 0)


try:
    Import("env")
except:
    # Default tools with no platform defaults to gnu toolchain.
    # We apply platform specific toolchains via our custom tools.
    env = Environment(tools=["default"], PLATFORM="")

env.PrependENVPath("PATH", os.getenv("PATH"))

# Custom options and profile flags.
customs = ["custom.py"]
profile = ARGUMENTS.get("profile", "")
if profile:
    if not profile.endswith('.py'):
        profile += '.py'

    path = str(Entry('#' + profile))

    if (os.path.isfile(path)):
        customs.append(path)
opts = Variables(customs, ARGUMENTS)
cpp_tool = Tool("godotcpp", toolpath=["tools"])
cpp_tool.options(opts, env)
opts.Update(env)

Help(opts.GenerateHelpText(env))

# Detect and print a warning listing unknown SCons variables to ease troubleshooting.
# But only do that if this is top level SConstruct, not subsidiary
if Dir("#").abspath == Dir(".").abspath:
    unknown = opts.UnknownVariables()
    if unknown:
        print("WARNING: Unknown SCons variables were passed and will be ignored:")
        for item in unknown.items():
            print("    " + item[0] + "=" + item[1])

scons_cache_path = os.environ.get("SCONS_CACHE")
if scons_cache_path is not None:
    CacheDir(scons_cache_path)
    Decider("MD5")

cpp_tool.generate(env)
library = env.GodotCPP()

Return("env")
