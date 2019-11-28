  @Override
  public Status read(String table, String key, Set<String> fields, Map<String, ByteIterator> result) {
    try {
      byte[] value = db.get(key.getBytes());
      Map<String, ByteIterator> deserialized = deserialize(value);
      result.putAll(deserialized);
    } catch (RocksDBException e) {
      System.out.format("[ERROR] caught the unexpceted exception -- %s\n", e);
      return Status.ERROR;
    }
    return Status.OK;
  }
  
  /**
  
  1. Why I use ByteIterator here?
  a.出于性能考虑，主要考虑字符串的成本、拷贝转码问题，流可能是一个图片（blob形式）
  b.byte是字节可以，可以屏蔽utf8、gbk等编码细节。文本从磁盘拿出来本来是二进制，需要通过编码转化为对应的字符。
    ByteIterator可以屏蔽不同服务器编码不一样的的问题。
  
  
  **/
