PROFILE := v3.5
SOURCEDIR := ../src/$(PROFILE)/

CSC ?= csc
CSC_COMMON_ARGS := -nologo -noconfig -optimize -nostdlib -unsafe -deterministic -publicsign -debug- -target:library -nowarn:612 -nowarn:618
Q_CSC = $(if $(V),,@echo "CSC   [$(PROFILE)] $(1)";)

ASSEMBLIES := Microsoft.Build.Engine Microsoft.Build.Framework Microsoft.Build.Tasks.v3.5 Microsoft.Build.Utilities.v3.5 Mono.XBuild.Tasks

# generated with (and then slightly tweaked):
# for i in *.dll; do ikdasm --assemblyref $i | grep Name= | sed 's/.*Name=//g' | sed -e $'s/$/\\\n/g' | sed -e ':a' -e 'N' -e '$!ba' -e 's/\n/ /g' -e "s/^/${i%.*}_REFS := /"; done
Microsoft.Build.Engine_REFS := ../v2.0/mscorlib ../v2.0/System ../v2.0/System.Xml Microsoft.Build.Framework Microsoft.Build.Utilities.v3.5 ../v2.0/System.Core
Microsoft.Build.Framework_REFS := ../v2.0/mscorlib
Microsoft.Build.Tasks.v3.5_REFS := ../v2.0/mscorlib Microsoft.Build.Utilities.v3.5 Mono.XBuild.Tasks Microsoft.Build.Framework ../v2.0/System ../v2.0/System.Core ../v2.0/System.Xml Microsoft.Build.Engine
Microsoft.Build.Utilities.v3.5_REFS := Microsoft.Build.Framework ../v2.0/mscorlib ../v2.0/System
Mono.XBuild.Tasks_REFS := ../v2.0/mscorlib ../v2.0/System.Xml ../v2.0/System

MONO_KEY := ../../../mcs/class/mono.pub
MSFINAL_KEY := ../../../mcs/class/msfinal.pub

MONO_KEY_ASSEMBLIES := Mono.XBuild.Tasks
MSFINAL_KEY_ASSEMBLIES := Microsoft.Build.Engine Microsoft.Build.Framework Microsoft.Build.Tasks.v3.5 Microsoft.Build.Utilities.v3.5

all: $(addsuffix .dll, $(ASSEMBLIES))

clean:
	rm -f *.dll

define KEYFILE_TEMPLATE
$(1)_KEYFILE := $(2)
endef

define ASSEMBLY_TEMPLATE
$(1).dll: $(addprefix $(SOURCEDIR),$(1).cs) $(addsuffix .dll, $($(1)_REFS)) $($(1)_KEYFILE) Makefile
	$(Q_CSC) $(CSC) -out:$(1).dll $(CSC_COMMON_ARGS) -keyfile:$($(1)_KEYFILE) $(addprefix -r:, $(addsuffix .dll, $($(1)_REFS))) $$<
endef

$(foreach asm, $(MONO_KEY_ASSEMBLIES),  $(eval $(call KEYFILE_TEMPLATE,$(asm), $(MONO_KEY))))
$(foreach asm, $(MSFINAL_KEY_ASSEMBLIES), $(eval $(call KEYFILE_TEMPLATE,$(asm), $(MSFINAL_KEY))))

$(foreach asm, $(ASSEMBLIES), $(eval $(call ASSEMBLY_TEMPLATE,$(asm))))