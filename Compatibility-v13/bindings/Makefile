MONO_ANDROID_PATH := /Developer/MonoAndroid/usr
ANDROID_SDK_PATH  := /opt/android/sdk
API_LEVELS        := 14
BUILDDIR          := bin
TOP               := ../..
thisdir           := Compatibility-v13/bindings

-include ../../env.config

MCW_GEN           := $(MONO_ANDROID_PATH)/bin/mcw-gen

platforms_dir := $(BUILDDIR)/platforms

ASSEMBLIES = $(API_LEVELS:%=$(platforms_dir)/android-%/Mono.Android.Compatibility.v13.dll)
DOCS       = $(API_LEVELS:%=$(platforms_dir)/android-%/Mono.Android.Compatibility.v13.xml)
JAR        = android-support-v13.jar

VPATH = $(ANDROID_SDK_PATH)/extras/android/support/v13/ $(ANDROID_SDK_PATH)/extras/android/compatibility/v13/

all:: $(JARS) $(ASSEMBLIES) $(DOCS)

clean::
	$(RM) -r $(ASSEMBLIES) obj

SOURCES = \
	$(TOP)/src/Android.Support.V4.Content/CursorLoader.cs \
	src/AssemblyInfo.cs

$(ASSEMBLIES): $(platforms_dir)/android-%/Mono.Android.Compatibility.v13.dll: $(JAR) map.csv methodmap.csv metadata $(SOURCES) $(MCW_GEN)
	-mkdir -p $(platforms_dir)/android-$*
	-mkdir -p obj/platform-$*
	$(MCW_GEN) \
		-sdk-dir="$(ANDROID_SDK_PATH)" \
		-sdk-platform=$* \
		-jar=$< \
		-dll="$(platforms_dir)/android-$*/Mono.Android.Compatibility.v13.dll" \
		-fixup=Metadata.xml \
		-enumfields=EnumFields.xml -enummethods=EnumMethods.xml -global -v \
		-o obj/platform-$* \
		-cscopt -debug+ -cscopt -nowarn:0109,0108 \
		-droiddoc=developer.android.com/reference \
		$(SOURCES:%=-cscopt "%")

$(DOCS): $(platforms_dir)/android-%/Mono.Android.Compatibility.v13.xml: $(platforms_dir)/android-%/Mono.Android.Compatibility.v13.dll map.csv metadata
	$(RM) -R obj/platform-$*/docs
	-mkdir -p obj/platform-$*/docs
	-cp -R Documentation/en obj/platform-$*/docs
	mdoc update -o obj/platform-$*/docs $< -L $(MONO_ANDROID_PATH)/lib/mandroid/platforms/android-$*
	mdoc export-msxdoc -o $@ obj/platform-$*/docs

$(MCW_GEN):
	@echo "Building bindings requires Mono for Android 4.2 or later."
	exit 1

$(JAR):
	@echo "Please install \"Extras > Android Support package\" within the \`android\` utility to build Compatibility-v13."
	@exit 1
