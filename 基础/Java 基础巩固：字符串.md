
String a  = “shixin” + 2;
//做了什么？

-----------

            String input = new String("yaomaiche".getBytes(),RestUtils.UTF8) ;

##String, byte[], Charset 关系？

##字符串和 byte[] 转换时的乱码问题

    public byte[] getBytes(Charset charset) {
        String canonicalCharsetName = charset.name();
        if (canonicalCharsetName.equals("UTF-8")) {
            return CharsetUtils.toUtf8Bytes(this, 0, count);
        } else if (canonicalCharsetName.equals("ISO-8859-1")) {
            return CharsetUtils.toIsoLatin1Bytes(this, 0, count);
        } else if (canonicalCharsetName.equals("US-ASCII")) {
            return CharsetUtils.toAsciiBytes(this, 0, count);
        } else if (canonicalCharsetName.equals("UTF-16BE")) {
            return CharsetUtils.toBigEndianUtf16Bytes(this, 0, count);
        } else {
            ByteBuffer buffer = charset.encode(this);
            byte[] bytes = new byte[buffer.limit()];
            buffer.get(bytes);
            return bytes;
        }
    }






http://www.cnblogs.com/xrq730/p/4841518.html

http://blog.csdn.net/u011240877/article/details/47256655

http://blog.csdn.net/zhangerqing/article/details/8093919

http://blog.csdn.net/u011240877/article/details/47259421