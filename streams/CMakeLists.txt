set(SRCFILES  dshr_methods_mod.F90
	      dshr_strdata_mod.F90
	      dshr_stream_mod.F90
	      dshr_tinterp_mod.F90)
set(MODFILES ${SRCFILES})
foreach(FILE ${SRCFILES})
  list(TRANSFORM MODFILES REPLACE ".F90" ".mod")
  if(EXISTS "${CASEROOT}/SourceMods/src.cdeps/${FILE}")
    list(REMOVE_ITEM SRCFILES ${FILE})
    list(APPEND SRCFILES "${CASEROOT}/SourceMods/src.cdeps/${FILE}")
    message("Using ${FILE} from ${CASEROOT}/SourceMods/src.cdeps")
  endif()
endforeach()
message("Stream srcfiles are ${SRCFILES}")
add_library(streams ${SRCFILES})
add_dependencies(streams FoX_dom)
if(BLD_STANDALONE)
  add_dependencies(streams cdeps_share)
endif()
target_include_directories (streams PUBLIC ${CMAKE_BINARY_DIR}/fox/include)
target_include_directories (streams PUBLIC ${ESMF_F90COMPILEPATHS})
target_include_directories (streams PUBLIC ${PIO_Fortran_INCLUDE_DIR})
target_include_directories (streams PUBLIC ${CMAKE_BINARY_DIR}/share)
install(TARGETS streams
	LIBRARY DESTINATION lib)
foreach(MOD ${MODFILES})
  install(FILES "${CMAKE_CURRENT_BINARY_DIR}/${MOD}" DESTINATION include)
endforeach()
